# PSO-Guide

Pure Service Orchestrator Documentation 

## How to Install

### Supported Install

- Setup Pure Service Orchestrator for Kubernetes[Install Helm and PSO](installation_PSO.md)
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

- [Quotas for a namespace Example](/quota-example/)
