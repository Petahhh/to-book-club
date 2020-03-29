# Section 2 - Core Concepts

## Recap on K8s Architecture

### Nodes vs Pods vs Containers

```
+------------------------------+
|                              |
|    +--------------------+    |
|    |                    |    |
|    | +--+Single App+--+ |    |
|    | |One app per POD | |    |
|    | |                | |    |
|    | +---+Container+--+ |    |
|    |                    |    |
|    | +---+(Helper)+---+ |    |
|    | | Optional Helper| |    |
|    | |                | |    |
|    | +---+Container+--+ |    |
|    |                    |    |
|    +-------+POD+--------+    |
|                              |
+------------+Node+------------+
     (VM or Physical Machine)


```

### Cluster

```
         +---->
         |
         |     +--------------------+    +--------------------+            +--------------------+
         |     |                    |    |                    |            |                    |
         |     |                    |    |                    |            |                    |
         |     |                    |    |                    |            |                    |
Cluster  |     |                    |    |                    |            |                    |
         |     |                    |    |                    |            |                    |
         |     |                    |    |                    |            |                    |
         |     +------+Node 1+------+    +------+Node 2+------+   +  +  +  +------+Node N+------+
         |
         +---->
```


### K8s Components

```
+-------------------------+    +-------------------------+    +-------------------------+
|        API Server       |    |       Controllers       |    |       Scheduler         |
+-------------------------+    +-------------------------+    +-------------------------+
|                         |    |                         |    |                         |
| - The interface for user|    | - Respond to changes in |    | - puts containers on    |
| configuration           |    | the cluster             |    | nodes                   |
+-------------------------+    +-------------------------+    +-------------------------+

+-------------------------+    +-------------------------+    +-------------------------+
|         ETCD            |    |    Container Runtime    |    |        Kubelet          |
+-------------------------+    +-------------------------+    +-------------------------+
|                         |    |                         |    |                         |
| - Distributed Key-Value |    | - Runs containers       |    | - Agent running on each |
| Store                   |    | (happens to be docker   |    | node in the cluster     |
| - Creates locks that can|    | but is configurable)    |    +-------------------------+
| prevent conflicting     |    +-------------------------+
| state between masters   |
+-------------------------+

```

### Kubectl

```
^                     ^
|                     |
+---------------------+
       Cluster

          ^
          |
          |
+---------+-----------+
|      Kubectl        |
+---------------------+
|                     |
| - CLI to issue      |
| commands to the a   |
| k8s cluster         |
+---------------------+

```

## Recap on PODS

`kubectl run nginx --image <image uri>` Creates a pod and deploys an image

`kubectl create -f pod-definition.yml` Creats a pod

`pod-definition.yml`
```
apiVersion: <version of the k8s api>
kind: <object your creating on k8s: e.g. pod>
metadata:
  name: <name you give your object>
  label: <user defined key value map which you can fill with information>
    my-label: youngMoney
spec: <key value map w/ specifications for your object>
```

`kubectl get pods` list the pods that are running

`kubtectl describe pod my-pod` get pod specific info

