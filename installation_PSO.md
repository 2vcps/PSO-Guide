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
**Helm is now tillerless! So that section is now gone!**
*RIP Tiller*

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
# OR
helm search pure-csi
```
You must create a values.yaml refer to readme link above for full set of options or use the sample pure.yaml for your FlashArray or FlashBlade management IP and API token information. [Sample Pure.yaml](/Samples/pure.yaml)

Dry run the installation to be sure your values.yaml is correct.

```
helm install pure-service-orchestrator pure/pure-k8s-plugin -f <your_own_dir>/yourvalues.yaml --dry-run --debug
# OR
helm install pure-service-orchestrator pure/pure-csi -f values.yaml --dry-run --debug
```

Now, run the install if it is 2019 or after use CSI unless you are on old K8s
```
helm install pure-service-orchestrator pure/pure-csi -f values.yaml --dry-run --debug
```

Check that PSO is fully running.

```
$ kubectl get pod
NAME                             READY   STATUS    RESTARTS   AGE
pure-csi-bcl6t                   3/3     Running   0          20h
pure-csi-k87fd                   3/3     Running   0          20h
pure-csi-krt7v                   3/3     Running   0          20h
pure-csi-r724p                   3/3     Running   0          20h
pure-csi-rtbkx                   3/3     Running   0          20h
pure-csi-sv6lj                   3/3     Running   0          20h
pure-csi-vxq2m                   3/3     Running   0          20h
pure-provisioner-0               3/3     Running   0          20h
```
### Storage Classes
List your Storage Classes
```
$ kubectl get storageclasses
NAME           PROVISIONER              AGE
cns-vvols      csi.vsphere.vmware.com   26d
fa-cns-vvols   csi.vsphere.vmware.com   26d
pure           pure-csi                 20h
pure-block     pure-csi                 20h
pure-file      pure-csi                 20h
```
By default PSO does not force any of the built in storage classes to be default. If you would like to make a StorageClass default it can be done with the following command. This will be the default class that will be used whenever a Persistent Volume Claim is made without specifying a storage class. 
_The "pure" StorageClass is to support older versions of the pure plugin and is the same as the "pure-block" class. It is reccomended to make pure-block or pure-file default if needed._
```
  kubectl patch storageclass pure-block -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
**OR**
  kubectl patch storageclass pure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

### Sample helm chart to test storage provisioning

Go Web App in the PSO-Guide [Demo Go Web App](gowebapp-demo/README.md)
```
kubectl apply -f 1-mysql-deployment-pvc.yaml
# wait for it to initialize the DB, if you are impatient it is ok the web app might fail and restart a few times until the DB responds.
kubectl apply -f 2-gowebapp-depployment-pvc.yaml
```


## Samples for Various distributions/installations of Kubernetes

One common reason for problems using PSO is small variances in where different distros of K8s set as the path for kubelet to look for volume plugins. This may result in the logs reporting no matching plugin, even after the volume is provisioned and bound to a PVC.

1. [Kubespray](/Samples/values-sample-kubespray.yaml)
2. [Openshift](/Samples/values-sample-openshift.yaml)
3. [Rancher (rke)](/Samples/values-sample-rancher.yaml)
4. [Kubeadm](Samples/values-sample-kubeadm.yaml) <- Techincally this is default with no changes for the Flex Path. 

Remember if you don't specifically override something in your local settings file. It will default to the values.yaml that is in the helm chart. You can see the current default file here in the Pure Storage Helm Repo. [values.yaml](https://github.com/purestorage/helm-charts/blob/master/pure-k8s-plugin/values.yaml)
