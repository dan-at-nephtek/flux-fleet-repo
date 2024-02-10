# flux-fleet-repo
Flux Fleet Repository, as per Flux Multi-tenancy docs.

To add a new cluster:

# Create a Personal Access Token in Githup
## Use classic PAT, select "public_repo" (or "repo" if accessing private repositories)
# Select a fleet repository.  Consider cloning from https://github.com/dan-at-nephtek/flux-fleet-repo
# Use `flux bootstrap` to bootstrap the cluster into the git repository:
```shell
$ flux bootstrap github \
  --owner=dan-at-nephtek \
  --repository=flux-fleet-repo \
  --branch=main \
  --path=./clusters/rancher-desktop \
  --personal
```
# At the prompt, provide your Personal Access Token from the first step

Your cluster now has been added to `flux-fleet-repository` using the user `dan-at-nephtek`,
on branch `main`, in the path `clusters/rancher-desktop` inside the repository, and using
a Personal Access Token for access to the git repository.

After a few minutes, you should see all of the flux pods in the `flux-system` namespace:

```shell
$ kubectl get pods -n flux-system
NAME                                       READY   STATUS    RESTARTS   AGE
kustomize-controller-7b7b47f459-7zht8      1/1     Running   0          8m20s
source-controller-7667765cd7-wx27l         1/1     Running   0          8m20s
notification-controller-5bb6647999-p8857   1/1     Running   0          8m20s
helm-controller-5d8d5fc6fd-fckjv           1/1     Running   0          8m20s
```

Additionally, in git you should see a new directory, `flux-system`, located in the 
repository, branch, and path provided in the `flux bootstrap` command.  If your cluster
was bootstrapped successfully, you should see three files in this directory:

```
gotk-components.yaml
gotk-sync.yaml
kustomization.yaml
```

These are the files that synchronize Flux itself to your cluster.  They should not be 
edited or touched after performing the `flux bootstrap` command.  

To add additional repositories and applications to Flux, we can add Flux kustomizations
to the base directory of the cluster.  In the example here, that would be in 
`clusters/rancher-desktop`.   As per the Flux Multi-tenancy docs (https://github.com/fluxcd/flux2-multi-tenancy),
applications and tenants may be provided by external repositories, while infrastructure
applications are defined and maintained in the fleet repository.   Overlays for values 
can be provided in the fleet repository, even for apps and tenants maintained externally,
providing centralized control and compliance for clusters, while delegating authority for
applications and tenants to users of the cluster.
