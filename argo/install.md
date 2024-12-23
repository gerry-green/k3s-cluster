Get basic install from argocd docs

```bash
k create ns argocd --dry-run=client -o yaml | k apply -f -
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
# configure ingress using tls passthrough but remove the explicit secret so it uses nginx default tls
```

