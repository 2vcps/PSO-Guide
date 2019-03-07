# PSO-Guide

Pure Service Orchestrator Documentation

## Sub-Path Fix for .snapshot error
There is a current issue when creating PVC's with Flashblade where the NFS filesystem is created but the POD goes into a crash loop or never starts.

One way to tell for this error from the CLI, check the logs of the pod:

```
kubectl logs flabby-mite-mysql-54d995b6cf-pffj9
chown: changing ownership of '/var/lib/mysql/.snapshot': Read-only file system
```

One way to fix this is using a subpath within the PVC. The moves the directory on the NFS filesystem that is mounted within the container. There is no need for the container to chown the .snapshot directory now as we are moved one level up in the folder structure.

The following example uses a subpath. Many helm charts also have a value available to have the PVC mounted in a subPath.

[Sub Path Sample](/sub-path/sub-path.md)

exerpt from this example:
```
containers:
      - name: master
        image: gcr.io/google_containers/redis:e2e  # or just image: redis
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        ports:
        - containerPort: 6379
        volumeMounts:
        - name: redis-master
          mountPath: /data
          subPath: master
      volumes:
      - name: redis-master
        persistentVolumeClaim:
          claimName: redis-pvc
```

Here is my example values.yaml for the stable/mysql helm chart to use a subPath. This is create a database1 folder within the PVC (NFS Filesystem) and mount that sub directory to the container and not the root / that contains the .snapshot directory:

```
persistence:
  enabled: true
  ## database data Persistent Volume Storage Class
  ## If defined, storageClassName: <storageClass>
  ## If set to "-", storageClassName: "", which disables dynamic provisioning
  ## If undefined (the default) or set to null, no storageClassName spec is
  ##   set, choosing the default provisioner.  (gp2 on AWS, standard on
  ##   GKE, AWS & OpenStack)
  ##
  storageClass: "pure-file"
  accessMode: ReadWriteOnce
  subPath: database1
  size: 8Gi
  annotations: {}
  ```
