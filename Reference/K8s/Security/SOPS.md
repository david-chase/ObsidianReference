#secrets #k8s #encryption 

```table-of-contents
```

## Example SOPS Encoded Manifest

- This is an example of a manifest encrypted with SOPS

``` yaml
apiVersion: v1
kind: Secret
metadata:
    name: sops-secret
    namespace: default
type: Opaque
stringData:
    username: ENC[AES256_GCM,data:Y2WRkAnRmw==,iv:1pB8eL6mFzHhX4V3I6yWp6Q=,tag:VzG2R9+0qA==,type:str]
    password: ENC[AES256_GCM,data:bkB7lXv3pQ==,iv:u9L2C6pE8I1Y9Z5S6X8Y7w==,tag:XyW3R1+1bA==,type:str]
sops:
    kms: []
    gcp_kms: []
    azure_kv: []
    hc_vault: []
    age:
        - recipient: age1slv9v9v9v9v9v9v9v9v9v9v9v9v9v9v9v9v9v9v9v9v9v9v9s0q0q0
          enc: |
            -----BEGIN AGE ENCRYPTED FILE-----
            YWdlLWVuY3J5cHRpb24ub3JnL3YxCi0+IFgyNTUxOSBtS096TmtvYm9vUk9Sck02
            RE9vR2R0T0t0T0t0T0t0T0t0T0t0T0t0T0t0T0t0T0t0T0t0T0t0T0t0T0t0T0sK
            -----END AGE ENCRYPTED FILE-----
    lastmodified: "2026-02-17T11:54:35Z"
    mac: ENC[AES256_GCM,data:aB1C2D3E4F5G6H7I8J9K0L,iv:mN0O1P2Q3R4S5T6U7V8W9X,tag:yZ0A1B2C3D4E5F6G7H8I9J,type:str]
    pgp: []
    unencrypted_suffix: _unencrypted
    version: 3.9.0
```

## Decryption Process for SOPS Secrets

The decryption of a SOPS-encoded Secret generally follows one of two paths: manual decryption via the CLI or automated decryption within the cluster using a controller.

### CLI Decryption

To decrypt the file manually on your local system using PowerShell, the SOPS binary uses the metadata stored in the `sops` section of the YAML to identify which private key is required. If the corresponding private key (such as an Age key file or a PGP key) is present on your machine, you run the following command:

`sops --decrypt secret.enc.yaml`

SOPS reads the encrypted data blocks, uses the private key to unlock the data encryption key (DEK), and then outputs the plaintext YAML.

### In-Cluster Decryption

Since you are using Argo CD on your tokyo cluster, decryption is typically handled by a plugin or operator so that the plaintext secrets are never stored in your GitHub repository.

- Argo CD SOPS Plugin: Argo CD can be configured with a custom sidecar or a plugin (like `argocd-vault-plugin`) that runs `sops --decrypt` during the manifest generation phase. The controller uses an environment variable or a volume-mounted key to perform the decryption before applying the Secret to the Kubernetes API.
    
- SopsSecretController or Flux: Some users deploy a controller that watches for a specific Custom Resource Definition (CRD). The controller detects the encrypted manifest, decrypts it using a key stored in a Kubernetes Secret (often the Age private key), and creates a standard Kubernetes Secret in the cluster.
    

In both scenarios, the Kubernetes API server eventually receives a standard, base64-encoded Secret. The "ENC[...]" strings are replaced with the actual values during the transition from the Git repository to the cluster's etcd storage.