# Section 8 - State Persistence

## Volumes

* Volumes can be attached to pods to retain the data the pod generates

ex. spec file with volumes. This makes files saved in /opt of the container persist on /data of the node

```
...
spec:
  ...
  containers:
  - image: alphone
    ...
    volumeMounts:
    - mountPath: /opt
      name: data-volume
  volumes:
  - name: data-volume
    hostPath:
      path: /data
      type: Directory
```

### Volume options

* `volumes[*].hostPath` doesn't work for multi nodes
* instead use other volume types such as
  `awsElasticBlockStore`
  
## Persistent Volumes

* Persistent volumes (PVs) is dedicated pool within the cluster that pods can use
* Pods submit Persistent Volume Claims (PVC) to reserve space in the PV

PV example
```
apiVersion: v1
kind: PersistentVolume
metadta:
  name: pv-vol1
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 1Gi
  hostPath: # do not use in production!
    path: /tmp/data
  
```

## Persistent Volume Claims

* PVCs are claimed to a single PV
* if a PVC is smaller than the PV it is assigned to, the extra space on the PV is lost because PVC's and PVs have a 1-1 relationship
* PVCs are in a pending state if there isn't a PV available
  * pending PVCs are bound to the created PV

PVC definition example:

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```


`spec.persistentVolumeReclaimPolicy` on PVs determine what happens when a PV is used and the PVC is deleted:
* `Retain` (not available to other PVCs again) (default)
* `Delete` (deletes it after being used)
* `Recycle` (cleaned and reused for another PVC)


## Using PVCs in PODs

`spec.volumes[*].persistentVolumeClaim.claimName` can be set on the pod definition so that the pod can a volume

e.g.
```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: myfrontend
    image: nginx
    volumeMounts:
    - mountPath: "/var/ww/html"
      name: mypd
  volumes:
  - name: mypd
    persistentVolumeClaim:
      claimName: myclaim
```

* The above can be used in replicasets/deployments container templates to attach PVs
