kubectl apply --server-side -f \
  https://raw.githubusercontent.com/cloudnative-pg/cloudnative-pg/release-1.24/releases/cnpg-1.24.2.yaml
wget https://github.com/cloudnative-pg/cloudnative-pg/releases/download/v1.24.2/kubectl-cnpg_1.24.2_linux_x86_64.deb \
  --output-document kube-plugin.deb
