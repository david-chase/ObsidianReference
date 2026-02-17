#secrets #k8s #encryption 

```table-of-contents
```

## Example SOPS Encoded Manifest

- Thi

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

