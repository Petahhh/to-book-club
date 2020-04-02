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
| - The interface for user|    | - Monitors objects and  |    | - puts containers on    |
| configuration           |    | respond accordingly     |    | nodes                   |
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

`kubtectl get pod <pod-name> -o yaml` to print pod definition

## Recap on Replica Sets


What happens if the app fails/pod fails? Redundancy helps. Replication Controller provides HA by running multiple PODs on the cluster. Replication Controller ensures the specified number of pods are runnings are all time. Key word running: even if you only have one pod, if that pod goes down the replication controller will revive it

Replication Controller can scale up number of PODs even across nodes

Replication Controller is being replaced Replica Set

### Replication Controller

`replication-controller-definition.yml` Example of a replication controler
```
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
spec:
  template: # Describes a pod template to be used by the controller
     <pod-definition.yml (just metadata + spec)>
  replicas: <number of replicas for this pod>
```

`kubectl create -f replication-controller-definition.yml` # Creates the pods via replication controller


### Replica sets

`replica-set-definition.yml` # Example definition yaml for a replica set

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    type: front-end
spec:
  template: # Describes a pod template to be used by replica set
     <pod-definition.yml (just metadata + spec)>
  replicas: <number of replicas for this pod>
  selector: # what pods fall under it - can create pods and use existing pods
    matchLabels:
      type: front-end # pods that much this label will be used
```

`selector` is required for Replica Sets

`kubectl create -f replica-set-deifnition.yml` # creates a replica set which then creates the pod

Why the selector??
* ensure existing pods stay up (deploys pods if they fail)
* labels help the replica set know which pods to monitor (there could be way more pods than running instances of the app you care about)
  * you can create a replica set even if the pods are already created, the labels help with this
  
Scaling the replica set:
Update your `replica-set-definition.yml` and run `kubectl replace -f replica-set-definition.yml`

or

`kubectl scale --replicas=6 -f replica-set-definition-original.yml`
`kubectl scale --replicas=6 <type> <name>` e.g. `kubectl scale --replicas=6 replicaset myapp-replicaset`


## Recap on Deployments

say you wanted to deploy many instances of your web app. A new version comes out and you want a rolling update (update some instances at a time) or you want a rollback if something goes bad. Maybe you want to make a change on one instance, observe it, then replace the rest of the instances.

deployment = replica set wrapper?

`deployment-definition.yml`

```
<same as replica set except for the kind field>
```

`kubectl create -f deployment-definition.yml` # creates the deployment

`kubectl get all` # shows all objects

Pro tip: use `kubectl run` to create objects without scaffolding out yaml files e.g.
`kubectl run --generator=run-pod/v1 nginx --image=nginx` # create a pod on the fly without yaml
`kubectl run --generator=run-pod/v1 nginx --image=nginx --dry-run -o yaml` # prints out yaml without creating it
`kubectl create deployment --image=nginx nginx` # create a deployment on the fly without yaml


## kubectl formatting tricks

`-o <something>` can be used to specify the format of the output:

```
kubectl something -o json
kubectl something -o yaml
kubectl something -o name # name only
kubectl something -o wide # print more stuff
```


## Namespaces Recap

* A `default` namespace is created where all your creations go unless specified
  * `kubectl get` commands list objects in the `default` namespace
  * `--namespace` flag can be used to specify the namespace
  * in the definition yaml you can also specify `metadata.namespace`
* K8s has it's own namespaces such as `kube-system` (private stuff) and `kube-public`
* You can create your own namespaces to isolate resources within one k8s cluster
* namespaces can have their own quotas and policies
* to reference a service from another namespace you would append it's namespace to it's name. k8s has it's own dns system. e.g. 
```
db-service.dev.svc.cluster.local
# db-service is the service name
# dev is the namespace
# svc indicates the type which is a service
$ cluster.local is the domain 
```

* namespaces can be created just like any other k8s object through a definition file or `kubectl create namespace someNewSpace`
* you can configure what namespace subsequent commands will use/refer to by running `kubectl config set-context $(kubectl config current-context) --namespace=dev`
* `--all-namespaces` can be added to your `kubectl get` commands

