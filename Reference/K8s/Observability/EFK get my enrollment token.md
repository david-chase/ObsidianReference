#efk #elasticsearch #token

``` powershell
# Create the token
kubectl -n efk-stack exec -it <elasticsearch pod name> -- bin/elasticsearch-create-enrollment-token -s kibana

# Get the verification code
kubectl logs -n efk-stack <kibana pod name>
```

