## Vault


```bash
kubectl exec -n vault vault-0 -- vault operator init \
    -key-shares=1 \
    -key-threshold=1 \
    -format=json > cluster-keys.json

export VAULT_UNSEAL_KEY=$(jq -r ".unseal_keys_b64[]" cluster-keys.json)
kubectl exec -n vault -ti vault-0 -- vault operator unseal $VAULT_UNSEAL_KEY
kubectl exec -n vault -ti vault-1 -- vault operator raft join http://vault-0.vault-internal:8200
kubectl exec -n vault -ti vault-1 -- vault operator unseal $VAULT_UNSEAL_KEY
kubectl exec -n vault -ti vault-2 -- vault operator raft join http://vault-0.vault-internal:8200
kubectl exec -n vault -ti vault-2 -- vault operator unseal $VAULT_UNSEAL_KEY
```
