#encryption #secrets #k8s 

https://github.com/getsops/sops

SOPS (Secrets OPerationS) is an open-source command-line tool designed for encrypting and decrypting structured files. In the context of Kubernetes, it is a primary solution for implementing GitOps workflows where sensitive configuration—such as Kubernetes Secrets—must be stored in version control systems like Git. Unlike tools that encrypt an entire file, SOPS is "format-aware" and typically encrypts only the values within a YAML or JSON file while leaving the keys in plaintext. This allows developers to maintain an audit trail of changes and perform meaningful diffs on encrypted files without exposing the actual sensitive data. It relies on external key management services or local keys to handle the underlying cryptography, acting as a flexible bridge between local development and secure cluster deployments.

## Key Features

- Structured Data Support: Native support for YAML, JSON, ENV, INI, and binary formats.
- Partial Encryption: Allows users to encrypt only specific fields (like data or stringData in a Kubernetes Secret) while keeping metadata like the secret name and labels visible for management.
- Cloud Integration: Supports major cloud Key Management Services (KMS) including AWS KMS, Google Cloud KMS, and Azure Key Vault.
- Alternative Key Providers: Supports open-source and local encryption methods such as age and PGP.
- GitOps Compatibility: Integrates with GitOps operators like Flux CD (natively) and Argo CD (via plugins like KSOPS) to automate the decryption of secrets directly within the cluster.
- Message Authentication: Uses a Message Authentication Code (MAC) to ensure that the encrypted file has not been tampered with or corrupted.
- Key Groups and Shamir's Secret Sharing: Allows for complex security policies where multiple keys or a threshold of keys from different providers are required to decrypt a single file.

## Technical Details

- In-place Editing: Provides a workflow where running the sops command opens the decrypted file in a text editor; once the editor is closed, the file is automatically re-encrypted and saved.
- Creation Rules: Utilizes a .sops.yaml configuration file to define which encryption keys should be used for specific file paths or patterns automatically.
- Encryption Mechanism: Employs envelope encryption where a unique data encryption key (DEK) is generated for each file and is itself encrypted by one or more master keys (KMS, PGP, or age).
- CLI Interface: Provides commands such as --encrypt, --decrypt, and --rotate to manage the lifecycle of encrypted documents.
- Kubernetes Operator: Can be extended via the sops-secrets-operator which creates native Kubernetes Secret objects from encrypted Custom Resources (SopsSecret).