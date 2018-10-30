# Installing PSO
If you are using K8s 1.7+ you must install helm using and RBAC account.
More information here
[Helm and RBAC](https://docs.helm.sh/using_helm/#role-based-access-control)

Also, certain OS's might have alternate installation methods. Helm must be working and the tiller pod must be running before you proceed to isntalling PSO or else it will fail.
[Helm Installation Documentation](https://docs.helm.sh/using_helm/#installing-helm)

### Installing Helm
Download the helm binary
[Helm Release for your client](https://github.com/kubernetes/helm/releases)


Unpack it (```tar -zxvf helm-v2.0.0-linux-amd64.tgz```)

Find the helm binary in the unpacked directory, and move it to its desired destination (```mv linux-amd64/helm /usr/local/bin/helm``` OR ```sudo mv linux-amd64/helm /usr/local/bin/helm``` )

You should now be able to run and get an output.
```
helm help
```

To install helm:
1. Download the [rbac-tiller.yaml](/Samples/rbac-tiller.yaml) file and create the service account and role binding for tiller.
```
$ kubectl create -f rbac-config.yaml
serviceaccount "tiller" created
clusterrolebinding "tiller" created
```
2. Then initialize helm from your client. This will setup tiller and your client to access the k8s cluster.
```
$ helm init --service-account tiller
```

Now you can run and receive similar output:
```
$ helm version
Client: &version.Version{SemVer:"v2.10.0", GitCommit:"9ad53aac42165a5fadc6c87be0dea6b115f93090", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.10.0", GitCommit:"9ad53aac42165a5fadc6c87be0dea6b115f93090", GitTreeState:"clean"}
```

Verify tiller is running which it should be if you received a "server" response above.
```
kubectl -n kube-system get pod
<snip>
tiller-deploy-56c4cf647b-jqzpw          1/1       Running   0          5d
</snip>
```

Now you may install PSO.

### Installing PSO

[Official PSO Repo and Readme](https://github.com/purestorage/helm-charts/tree/master/pure-k8s-plugin)

Add the Pure Helm chart repo to your helm configuration:
```
helm repo add pure https://purestorage.github.io/helm-charts
helm repo update
```
Verify it is adding correctly by searching for the Pure K8s plugin (aka PSO)
```
helm search pure-k8s-plugin
```
You must create a values.yaml refer to readme link above for full set of options or use the sample pure.yaml for your FlashArray or FlashBlade management IP and API token information. [Sample Pure.yaml](/Samples/pure.yaml)

Dry run the installation to be sure your values.yaml is correct.

```
$ helm install --name pure-service-orchestrator pure/pure-k8s-plugin -f <your_own_dir>/yourvalues.yaml --dry-run --debug
```

Now, run the install
```
$ helm install --name pure-service-orchestrator pure/pure-k8s-plugin -f <your_own_dir>/yourvalues.yaml
```

Check that PSO is fully running.

```
$ kubectl get pod
NAME                                READY     STATUS    RESTARTS   AGE
pure-flex-2tbzg                     1/1       Running   0          4d
pure-flex-5nn4g                     1/1       Running   0          4d
pure-flex-6hc7f                     1/1       Running   0          4d
pure-flex-6qzlf                     1/1       Running   0          4d
pure-flex-7zzvk                     1/1       Running   0          4d
pure-flex-fnp26                     1/1       Running   0          4d
pure-flex-kwhj6                     1/1       Running   0          4d
pure-flex-mfrvl                     1/1       Running   0          4d
pure-flex-mp8ms                     1/1       Running   0          4d
pure-flex-pm25s                     1/1       Running   0          4d
pure-flex-rgxbv                     1/1       Running   0          4d
pure-provisioner-5554b5bcdc-wtdnr   1/1       Running   0          4d
```
### Storage Classes
List your Storage Classes
```
$ kubectl get storageclasses
NAME         PROVISIONER        AGE
pure         pure-provisioner   4d
pure-block   pure-provisioner   4d
pure-file    pure-provisioner   4d
```
By default PSO does not force any of the built in storage classes to be default. If you would like to make a StorageClass default it can be done with the following command. This will be the default class that will be used whenever a Persistent Volume Claim is made without specifying a storage class. 
_The "pure" StorageClass is to support older versions of the pure plugin and is the same as the "pure-block" class. It is reccomended to make pure-block or pure-file default if needed._
```
  kubectl patch storageclass pure-block -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
**OR**
  kubectl patch storageclass pure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

### Sample helm chart to test storage provisioning

I use the helm chart for minecraft because it is a small sample java app that will mount a directory from a PVC with the following command to get started:
```
helm install -n mc-demo stable/minecraft --set minecraftServer.eula=true
```


## Samples for Various distributions/installations of Kubernetes

One common reason for problems using PSO is small variances in where different distros of K8s set as the path for kubelet to look for volume plugins. This may result in the logs reporting no matching plugin, even after the volume is provisioned and bound to a PVC.

1. [Kubespray](/Samples/values-sample-kubespray.yaml)
2. [Openshift](/Samples/values-sample-openshift.yaml)
3. [Rancher (rke)](/Samples/values-sample-rancher.yaml)
4. [Kubeadm](Samples/values-sample-kubeadm.yaml) <- Techincally this is default with no changes for the Flex Path. 

Remember if you don't specifically override something in your local settings file. It will default to the values.yaml that is in the helm chart. You can see the current default file here in the Pure Storage Helm Repo. [values.yaml](https://github.com/purestorage/helm-charts/blob/master/pure-k8s-plugin/values.yaml)