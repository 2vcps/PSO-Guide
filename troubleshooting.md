# Troubleshooting PSO

- Are the PSO pods running (pure-flex and pure-provisioner)?
   ```
   kubectl get pod
   NAME                                  READY     STATUS    RESTARTS   AGE
   pure-flex-744cr                       1/1       Running   0          1d
   pure-flex-94bm8                       1/1       Running   0          1d
   pure-flex-jgqxr                       1/1       Running   0          1d
   pure-flex-jkk5q                       1/1       Running   0          1d
   pure-flex-jkl68                       1/1       Running   0          1d
   pure-flex-lwvmq                       1/1       Running   0          1d
   pure-flex-nkhr6                       1/1       Running   0          1d
   pure-flex-psd46                       1/1       Running   0          1d
   pure-flex-qktw4                       1/1       Running   0          1d
   pure-flex-qrmvk                       1/1       Running   0          1d
   pure-flex-tj4fs                       1/1       Running   0          1d
   pure-provisioner-6f8bb8769b-kjg8z     1/1       Running   0          1d
   ```
   There will be one pure-flex pod per worker node and one pure-provisioner for the cluster. Verify it is running and ready "1/1"

- Look at the Persistent Volume Claims created. Sample pvc here [pure-block.yaml](/Samples/pure-block.yaml) or [pure-file.yaml](/Samples/pure-file.yaml)
   ```
   kubectl get pvc
   NAME                       STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
   redis-master-claim         Bound     pvc-65724020-bbae-11e8-a9d4-0050569f373f   2Gi        RWO            pure-block     1d
   redis-master-claim-label   Bound     pvc-88615c1a-bbae-11e8-a9d4-0050569f373f   2Gi        RWO            pure-block     1d
   redis-slave-claim          Bound     pvc-6573fb36-bbae-11e8-a9d4-0050569f373f   2Gi        RWO            pure-block     1d
   redis-slave-claim-label    Bound     pvc-8862c4f4-bbae-11e8-a9d4-0050569f373f   2Gi        RWO            pure-block     1d
   ```
   When the Pure-provisioner successfully creates the volume or file system the Status will read "Bound". The Volume column will provide a name that will correspond to the FlashArray or FlashBlade.


   If the status is "Pending" run these two commands:
   ```
   kubectl describe pvc <pvc name>
   kubectl logs pure-provision-<unique id>
   ```
   Depending on the output there are a few causes of this behaviour.
   * Array is Fiber Channel but helm installation is set to iSCSI
   * iSCSI stack on linux host is not using unique IQN or other open-iSCSI issue.
- Is the PVC "Bound" but the POD mounting the PVC stuck at "pending"?
   * Previous two bullets are correct but the volume is still pending with no specific error from the ```kubectl describe pvc <pvc name>``` or ```kubectl describe pod <pod name>```
      * This can be due ot the kubelet not finding the flex-volume driver on the worker node. To verify this is the issue.
      ```
      kubectl get pod <pod name> -o wide
      ```
      This will output the host trying to start the pod. Please ssh into this host and:
      
      ```
      tail /var/log/syslog
      ```
      Look for "no matching plugin" <jo find exact error>
      If this is in the logs this means the kubelet is unable to find the plugin at the path it uses for plugins. Please refer to the installation intructions for PSO on modify the flex install path to match your installation. 
      [Install PSO Document](installation_PSO.md)
      


