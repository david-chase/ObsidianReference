#aws #lambda #ecr #secrets #k8s #security

## ✅ Goal
Allow Kubernetes pods in your `tokyo` cluster to pull private images from AWS ECR using a short-lived token that’s automatically rotated and synced via External Secrets Operator (ESO).

---

## 🔧 Components Configured

### 1. AWS Secrets Manager
- **Secret name**: `ecr-creds`
- **Purpose**: Stores a Kubernetes-compatible `.dockerconfigjson` for imagePullSecrets
- **Rotation**: Enabled, with daily auto-rotation

### 2. Lambda Function: `rotate-ecr-secret`
- **Runtime**: Python 3.12
- **Function**: 
  - Gets a new ECR token
  - Formats it as `.dockerconfigjson`
  - Writes it to the `ecr-creds` secret

### 3. IAM Role: `dchase_LambdaRole`
- **Trust Policy**:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

- **Permissions Policy**:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["ecr:GetAuthorizationToken"],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:GetSecretValue",
        "secretsmanager:PutSecretValue"
      ],
      "Resource": "arn:aws:secretsmanager:ca-central-1:284265326267:secret:ecr-creds*"
    }
  ]
}
```

---

## 🧠 Lambda Function Code (rotate-ecr-secret.py)

```python
import boto3
import base64
import json
import logging

logger = logging.getLogger()
logger.setLevel(logging.INFO)

ecr_client = boto3.client('ecr')
secretsmanager_client = boto3.client('secretsmanager')

def lambda_handler(event, context):
    secret_arn = event["SecretId"]
    token = get_ecr_token()
    dockerconfig = make_dockerconfig(token)
    wrapped = {
        ".dockerconfigjson": json.dumps(dockerconfig)
    }
    secretsmanager_client.put_secret_value(
        SecretId=secret_arn,
        SecretString=json.dumps(wrapped)
    )
    logger.info("Successfully rotated secret for: %s", secret_arn)

def get_ecr_token():
    response = ecr_client.get_authorization_token()
    auth_data = response['authorizationData'][0]
    token = base64.b64decode(auth_data['authorizationToken']).decode('utf-8')
    username, password = token.split(':')
    registry = auth_data['proxyEndpoint']
    return { "username": username, "password": password, "registry": registry }

def make_dockerconfig(token_info):
    auth = f"{token_info['username']}:{token_info['password']}"
    b64_auth = base64.b64encode(auth.encode('utf-8')).decode('utf-8')
    return {
        "auths": {
            token_info['registry']: {
                "username": token_info['username'],
                "password": token_info['password'],
                "auth": b64_auth
            }
        }
    }
```

---

## ☁️ Deploy Lambda Function

```powershell
Compress-Archive -Path rotate-ecr-secret.py -DestinationPath rotate-ecr-secret.zip -Force

aws lambda create-function `
  --function-name rotate-ecr-secret `
  --runtime python3.12 `
  --handler rotate-ecr-secret.lambda_handler `
  --zip-file fileb://rotate-ecr-secret.zip `
  --role arn:aws:iam::284265326267:role/dchase_LambdaRole
```

To update later:

```powershell
aws lambda update-function-code `
  --function-name rotate-ecr-secret `
  --zip-file fileb://rotate-ecr-secret.zip
```

---

## 🔐 Enable Secrets Manager Rotation

```powershell
aws lambda add-permission `
  --function-name rotate-ecr-secret `
  --statement-id AllowSecretsManagerInvocation `
  --action lambda:InvokeFunction `
  --principal secretsmanager.amazonaws.com `
  --source-account 284265326267
```

```powershell
aws secretsmanager rotate-secret `
  --secret-id ecr-creds `
  --rotation-lambda-arn arn:aws:lambda:ca-central-1:284265326267:function:rotate-ecr-secret `
  --rotation-rules AutomaticallyAfterDays=1
```

---

## 🧪 Manual Test Steps

### Rotate the secret now:
```powershell
aws secretsmanager rotate-secret --secret-id ecr-creds --rotate-immediately
```

### Trigger ESO sync:
```powershell
kubectl annotate externalsecret ecr-creds -n densify `
  reconcile.eso.external-secrets.io/requested-at="$(Get-Date -Format o)"
```

### ✅ Success message received:
```
ExternalSecret
ecr-creds
densify
Updated
secret updated
```
