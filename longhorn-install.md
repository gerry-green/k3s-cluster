```bash
# install isci on all nodes:
kubectl apply -f https://raw.githubusercontent.com/longhorn/longhorn/v1.4.0/deploy/prerequisite/longhorn-iscsi-installation.yaml

# install nfs-common on all nodes:
for node in $(kubectl get no -o json | jq -r '.items[]|.metadata.name'); do ssh $node sudo apt-get install -y nfs-common; done

# disable multipath on devices used by longhorn - this applies only to the RPIs.
# Note should check the existing multipath.conf on a pi first - this presumes the default is still just the friendly name

cat <EOF >> multipath.conf
defaults {
    user_friendly_names yes
}
blacklist {
    devnode "^sd[a-z0-9]+"
}
EOF

for node in $(kubectl get no -o json | jq -r '.items[]|select(.metadata.labels."kubernetes.io/arch" == "arm64")|.metadata.name'); do
   scp multipath.conf $node:
   ssh $node sudo chown root:root multipath.conf
   ssh $node sudo mv multipath.conf /etc
   ssh $node sudo systemctl restart multipathd.service
   ssh $node sudo multipath -t | grep -A2 blacklist | grep devnode
done

rm multipath.conf

# Install longhorn via helm chart
helm repo add longhorn https://charts.longhorn.io
helm repo update
helm install longhorn longhorn/longhorn --namespace longhorn-system --create-namespace --version 1.4.0
```