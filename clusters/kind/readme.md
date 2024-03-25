First we need to create a cluster using `kind`.  Create a file named `kind-config.yaml`, 
with the contents as:
```yaml
# three node (two workers) cluster config
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 30080
    hostPort: 80
    protocol: TCP
  - containerPort: 30443
    hostPort: 443
    protocol: TCP
- role: worker
- role: worker
```

From there, we can use this to create a 3-node cluster using `kind`:

```shell
kind create cluster --name kind --config kind-config.yaml
```

Following this, you need to bootstrap the cluster to Flux.  You'll need your GitHub
Personal Access Token:
```shell
flux bootstrap github \                                                                         
  --token-auth \
  --owner=dan-at-nephtek \
  --repository=flux-fleet-repo \
  --branch=main \
  --path=./clusters/kind \
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
