# PSO-Guide

Pure Service Orchestrator Documentation

## How to Install

### Supported Install

- Setup Pure Service Orchestrator for Kubernetes [Install Helm and PSO](installation_PSO.md)
- Sample pure.yaml file -> [pure.yaml](/Samples/pure.yaml)
- How to install for Docker / Docker Swarm -> [Docker and Swarm](docker.md)

### Manual (unsupported non-helm install)

- Don't do this yet.

### How to Operate

- [New PVC from a Snapshot](/Samples/fromsnap.yaml)
- [New PVC from existing PVC](Samples/frompvc.yaml)
- [New PVC from existing Volume](Samples/fromVol.yaml) **Note, this is will import and rename a volume to be used by PSO.**

### How to Troubleshoot

- [Troubleshooting Doc](troubleshooting.md)

Continuing to have problems? Let me know.

### Storage Quotas

Not PSO but related.

- [Quotas for a namespace Example](/quota-example/quotas.md)

### Using  a sub path

Sometimes on a FlashBlade you may want to use a single PVC that will mount a single NFS Export. Then each container mounts a unique subfolder within the PVC. This requires the PVC to be RWX. Here is a sample [Sub Path Sample](/sub-path). I am not saying this is the best way or even the right way. Just a way do it if you really need to. This is not a specific PSO function but uses the RWX support on Flashblade to enable an existing option within K8s.
