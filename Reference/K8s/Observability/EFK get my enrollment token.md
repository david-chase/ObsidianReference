#efk #elasticsearch #token

``` powershell
# Create the token
kubectl -n efk-stack exec -it <elasticsearch pod name> -- bin/elasticsearch-create-enrollment-token -s kibana

# Get the verification code
kubectl logs -n efk-stack <kibana pod name>

# Generate a new kibana_system password
kubectl -n efk-stack exec -it <elasticsearch pod name> -- bin/elasticsearch-reset-password -u kibana_system

# Create a user and password to access kibana
kubectl -n efk-stack exec -it elasticsearch-74cc869876-clp6q -- bin/elasticsearch-users useradd dchase -p "yM73txhrUpRJ" -r kibana_admin

# Generate (and output) password for "elastic" superuser
kubectl -n efk-stack exec -it elasticsearch-74cc869876-clp6q -- bin/elasticsearch-reset-password -u elastic
# bRe4GdnY35g49xUHE6dz

```

