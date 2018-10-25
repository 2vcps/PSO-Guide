# PSO and Docker

## Pure Storage Linux Recommended Settings

Always make sure these are set first.
[Linux Recommended Settings](https://support.purestorage.com/Solutions/Linux/Reference/Linux_Recommended_Settings)

### Operating Systems Supported*

- CentOS 7*
- RedHat RHEL7
- Ubuntu (Trusty 14.04 LTS, Xenial 16.04 LTS)

### Environments Supported*

- Docker (v17.06 and above)
- Swarm
- Mesos 1.8 and above

### Other software dependencies

- Latest linux multipath software package for your operating system (Required)
- Latest iSCSI initiator software for your operating system (Optional, required for iSCSI connectivity)
- Latest NFS software package for your operating system (Optional, required for NFS connectivity)
- Latest FC initiator software for your operating system (Optional, requied for FC connectivity)
- Latest Filesystem utilities/drivers (XFS by default, Required)

### FlashArray and FlashBlade

The FlashArray and/or FlashBlade should be connected to the docker compute nodes using Pure's best practices

- *Please see release notes for details*

## Installing the Docker Plugin

There are two places for the docker plugin

- [Docker Hub](https://hub.docker.com/r/purestorage/docker-plugin/)
- [Docker Store](https://store.docker.com/plugins/pure-docker-volume-plugin)

In general fixes can be pushed to the Docker Hub link much faster than the Store. So to obtain the latest version of the plugin please use the Docker Hub.

### Create the pure.json

Below is an example pure.json file for the settings.
Create this file with your own API token and IP management.

```json
{
       "FlashArrays":[
           {
               "MgmtEndPoint":"1.2.3.4",
               "APIToken":"661f9687-0b1e-7b0d-e07d-1e776d50f9eb",
               "Labels":{
                   "env": "production",
                   "rack": "1b"
               }
           },
           {
               "MgmtEndPoint":"5.6.7.8",
               "APIToken":"661f9687-0b1e-7b0d-e07d-1e776d50f9eb",
               "Labels":{
                   "env": "staging",
                   "rack": "2a"
               }
           }
       ],
       "FlashBlades":[
           {
               "MgmtEndPoint":"1.2.3.4",
               "NFSEndPoint":"1.2.3.5",
               "APIToken":"T-661f9687-0b1e-7b0d-e07d-1e776d50f9eb"
           }
       ]
   }
```

Create the directory for pure.json and copy your file there.

```bash
sudo mkdir /etc/pure-docker-plugin
sudo cp pure.json /etc/pure-docker-plugin
```

If you are using multiple hosts for mesos or swarm you will have to hope this file to each node. I use ansible to do this.

### Install the Docker plugin

This  will insall the plugin and let you confirm the permissons required for the plugin to run.

```bash
docker plugin install store/purestorage/docker-plugin:3.3 --alias pure
```

To do an automated install without confirming the permissions and accepting all the settings.

```bash
docker plugin install store/purestorage/docker-plugin:3.3 --alias pure --grant-all-permissions
```