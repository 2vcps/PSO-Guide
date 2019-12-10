# PSO-Guide

Pure Service Orchestrator Documentation

## How to Install

### Supported Install

- Use the helm or Operator Install for CSI - [CSI Helm](https://github.com/purestorage/helm-charts/tree/master/pure-csi) or [CSI Operator](https://github.com/purestorage/helm-charts/tree/master/operator-csi-plugin)
- Use the helm or Operator Install for FlexVolume - [Flex Helm](https://github.com/purestorage/helm-charts/tree/master/pure-k8s-plugin) or [Flex Operator](https://github.com/purestorage/helm-charts/tree/master/operator-k8s-plugin)
- How to install for Docker / Docker Swarm -> [Docker and Swarm](docker.md)

### How to Operate

**This is for the FlexVolume Driver only**
- [New PVC from a Snapshot](/Samples/fromsnap.yaml)
- [New PVC from existing PVC](Samples/frompvc.yaml)
- [New PVC from existing Volume](Samples/fromVol.yaml) **Note, this is will import and rename a volume to be used by PSO.**

**Snapshot and clones for CSI**

Additional Information for snapshots and CSI
[From Helm](https://github.com/purestorage/helm-charts/tree/master/pure-csi#csi-snapshot-and-clone-features-for-kubernetes)
[From Operator](https://github.com/purestorage/helm-charts/tree/master/operator-csi-plugin#csi-snapshot-and-clone-features-for-kubernetes)

### PKS Enterprise Deployment Guide
[PKS Enterprise Deployment Guide](PureStorage_and_PKS_Enterprise.pdf)
[VMware Marketplace](https://marketplace.vmware.com/vsx/solutions/pure-service-orchestrator-2-5-2?ref=search)


### How to Troubleshoot

- [Troubleshooting Doc](troubleshooting.md)

Continuing to have problems? Let me know.

### Storage Quotas

Not PSO but related.

- [Quotas for a namespace Example](quota-example/quotas.md)

### Using  a sub path

Sometimes on a FlashBlade you may want to use a single PVC that will mount a single NFS Export. Then each container mounts a unique subfolder within the PVC. This requires the PVC to be RWX. Here is a sample [Sub Path Sample](/sub-path/sub-path.md). I am not saying this is the best way or even the right way. Just a way do it if you really need to. This is not a specific PSO function but uses the RWX support on Flashblade to enable an existing option within K8s.
