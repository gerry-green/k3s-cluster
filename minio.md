# Minio Operator

Exploratory - using minio as s3 storage for grafana loki

Note minio operator install requires krew which stalled at version 0.43 over a year ago. While minio abandandoned their helm chart over two years ago and switched to an operator and a weird kubectl based plugin install. Who knew helm could install operators too?

### [Add Krew plugin to kubectl](https://krew.sigs.k8s.io/docs/user-guide/setup/install/)

no, seriously this is how you install Krew:
```bash
(
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  KREW="krew-${OS}_${ARCH}" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
  tar zxvf "${KREW}.tar.gz" &&
  ./"${KREW}" install krew
)
```

And you still have to add the krew install to your path manually (add to .bashrc or .zshrc and restart shell):

```bash
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"
```

Next install minio plugin:

```bash
kubectl krew install minio
```

Note this warning that comes back - again this lack of polish is indicative of something that was started and abandoned. May need to revisit loki storage:

WARNING: You installed plugin "minio" from the krew-index plugin repository.
   These plugins are not audited for security by the Krew maintainers.
   Run them at your own risk.

Initialize the minio operator
```bash
kubectl minio init
```

Create the minio ingress (also add minio.g2dev.net to rpi DNS)
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minio-ingress
  namespace: minio-operator
spec:
  ingressClassName: nginx
  tls:
  - hosts:
      - minio.g2dev.net
  rules:
  - host: minio.g2dev.net
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: console
            port:
              number: 9090
```

Grab the secret to login to the minio console
```bash
kubectl get secret/console-sa-secret -n minio-operator -o json | jq -r '.data.token' | base64 -d|clip
```
