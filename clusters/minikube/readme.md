To create the cluster:

```shell
minikube start --kubernetes-version=1.28.3 --memory=24000m --cpus='6' --ports=80:30080,443:30443
```
This starts a new K8s cluster using version 1.28.3, with 24G of memory, 6vCPUs, 
and forwarding localhost ports 80/443 to the node ports 30080/30443.

Following this, you need to bootstrap the cluster to Flux.  You'll need your GitHub 
Personal Access Token:
```shell
flux bootstrap github \                                                                         
  --token-auth \
  --owner=dan-at-nephtek \
  --repository=flux-fleet-repo \
  --branch=main \
  --path=./clusters/minikube \
  --personal
```

Don't forget to create the SOPS GPG secret:
```shell
gpg --export-secret-keys --armor "${KEY_FP}" |
kubectl create secret generic sops-gpg \
  --namespace=flux-system \
  --from-file=sops.asc=/dev/stdin
```

From there you will have a cluster with cert-manager, ingress-nginx, and the grafana/loki/tempo
stack.
