[mysql documentation](https://dev.mysql.com/doc/mysql-operator/en/mysql-operator-installation-helm.html)

```bash
helm repo add mysql-operator https://mysql.github.io/mysql-operator/
helm repo update
helm install my-mysql-operator mysql-operator/mysql-operator --namespace mysql-operator --create-namespace 
```

Install an innodb cluster
```bash
helm install mysql-hoarder mysql-operator/mysql-innodbcluster -n mysql-hoarder --version 2.0.8 --set credentials.root.password="dahotwu" --set tls.useSelfSigned=true --create-namespace
```