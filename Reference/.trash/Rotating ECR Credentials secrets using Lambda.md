#aws #lambda #ecr #secrets #k8s #security

## ✅ Summary: How We Enabled ECR Credential Rotation for Kubernetes

### 🎯 **Objective**

Allow Kubernetes pods to pull private images from AWS Elastic Container Registry (ECR), using short-lived credentials that are automatically rotated and synced into your cluster via External Secrets Operator (ESO).

---

## 🔧 Components We Configured

### 1. **AWS Secrets Manager**

- We created a secret named `ecr-creds`.
    
- This secret stores a `.dockerconfigjson` format compatible with Kubernetes imagePullSecrets.
    

### 2. **AWS Lambda Function (`rotate-ecr-secret`)**

- This function:
    
    - Calls `ecr:GetAuthorizationToken`
    - Decodes it into Docker login credentials
    - Wraps it as `.dockerconfigjson`
    - Saves it to the `ecr-creds` secret
        

### 3. **IAM Role for Lambda (`dchase_LambdaRole`)**

- We created this role with permission to:
    
    - Call `ecr:GetAuthorizationToken`
        
    - Read/write to `ecr-creds` in Secrets Manager
        
- We added a **trust relationship** so Lambda could assume the role.
    
- We added a **resource-based policy** so **Secrets Manager** could invoke the Lambda.
    

### 4. **Secrets Manager Rotation Setup**

- We enabled **automatic rotation** for the `ecr-creds` secret using:
    
    powershell
    
    CopyEdit
    
    ``aws secretsmanager rotate-secret `   --secret-id ecr-creds `   --rotation-lambda-arn arn:aws:lambda:ca-central-1:284265326267:function:rotate-ecr-secret `   --rotation-rules AutomaticallyAfterDays=1``
    

---

## ✅ Final Validation and Manual Tests

We successfully ran:

### 🔄 1. Manual Rotation Trigger

powershell

CopyEdit

`aws secretsmanager rotate-secret --secret-id ecr-creds --rotate-immediately`

### 🔁 2. ESO Reconciliation

powershell

CopyEdit

``kubectl annotate externalsecret ecr-creds -n densify `   reconcile.eso.external-secrets.io/requested-at="$(Get-Date -Format o)"``

These tests confirmed:

- The Lambda rotated the credentials correctly.
    
- ESO re-synced the new credentials into Kubernetes.
    
- Image pulls from ECR started succeeding again.