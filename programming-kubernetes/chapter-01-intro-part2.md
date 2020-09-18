# Controllers and Operators

## Controllers

Controllers implement a control loop. They watch the shared state of the cluster through the API server and make changes
to move from the current state to the desired state. They act on resources, both core resources, such as deployments
and services, and custom-defined resources.

As an example, the `ReplicaSet` controller would compute the difference of number of pods between specification and
current state and then implement two cases depending on that. Some pseudocode to illustrate it:
```
difference = numberOfPods - numberOfReplicas;
if (diff < 0) {
    // create more pods
} else (if diff > 0) {
    // destroy some pods
    ...
    // strategy to decide which pods to delete
    ...
}
```

Resources to be change don't necessarily need to live inside of the cluster. We could manage external resources using
a controller, such as an S3 bucket.

### The control loop

Generally looks as follows:
1. Read the state of resources, preferably in an event-driven way.
1. Change the state of objects in the cluster, or in the cluster-external world. Examples of changing state could be
launching a pod, creating a network endpoint, or querying a cloud API.
1. Update the status of the resource described in step 1 via the API server in `etcd`.
1. Repeat cycle; return to step 1.

Reading resource state, changing the world, and updating resource status are steps that remain constant in controllers,
regardless of its complexity. The diagram below illustrates the control loop, with the main loop of the controller in
the middle.

```
+------------+                                 +---------------+
|            |                                 |               |
|    User    |                                 | Resync period |
|            |                                 |               |
+-----+------+                                 +-------+-------+
      |                                                |
      |modifies                                        |reconcile
      v                 get notified about             v
+-----+----------+    changes via informers    +-------+-------+                            +------------------+
|     Custom     +---------------------------->+               | make world look as desired |                  |
| resources with |                             |   Controller  +--------------------------->+  External world  |
| desired state  +<----------------------------+               |                            |                  |
+----------------+      update status          +--+---------+--+                            +------------------+
                                                  ^         |
                                                  |         |
                                                  |         |
                                                  |         |
                                                  +---------+
                                                  requeue on
                                                    errors
```


The main loop is continuously running inside of the controller process, which usually runs inside a pod in the cluster.

A controller typically uses two data structures:
- **Informers**: data structures that watch desired state and implement a resync mechanism to prevent the cluster state
and the assumed state from drifting.
- **Work queue**: data structures used by the event handler to queue state changes and implement retries.

### Events

Events are heavily employed by Kubernetes. They help implement a loosely coupled component design, and trigger business
logic in controllers.

Events can be edge-driven or level-driven. Edge-driven events triggers handlers whenever a change occurs, whereas
level-driven events act like polling loops that check conditions. Edge-driven events scale well for large amount of
objects, whereas level-driven do not due to latency.

Because events are not guaranteed to be ordered or even successfully delivered in distributed systems, it is necessary
to implement strategies to avoid losing events. Most Kubernetes controllers combine edge-triggered events with
level-driven business logic execution and add a continuous resync to it, in order to get the best out of each strategy.

The diagram below illustrates how events trigger business logic execution in Kubernetes:
```
                                                              +------------------+
 +----------+                                 puts the pod    |                  |
 |          |                                  +------------->+ Scheduling queue |
 |   User   |                            +-----------+        |                  |
 |          |                            |           |        +------------------+
 +-----+----+                            | Scheduler |
       |                                 |           |
       | creates a                       +-----+-----+
       | deployment                            |
       v                                       | notices the pod
 +-----+----+                                  v
 |          |                            +-----+----+
 | Informer |                            |          |
 |          |                            | Informer |
 +-----+----+                            |          |
       ^                                 +-----+----+
       | notices new                           ^
       | deployment                            | creates a pod
       |                                       |
+------+-----+                          +------+-----+
|            |                          |            |
| Deployment |                          | ReplicaSet |
| controller |                          | controller |
|            |                          |            |
+------+-----+                          +-----+------+
       |          +----------+                |
       |          |          |                |
       +--------->+ Informer +<---------------+
  creates a       |          |     notices new
  replica set     +----------+     replica set

========================================================================================================================

  +----------+
  |          |
  | Informer |
  |          |
  +-----+----+
        ^
        | notices the pod
        | in the scheduling
        | queue
        |
  +-----+----+
  |          |
  | kubelet  |
  |          |
  +-----+----+
        |
        |
        |
        |
        v
   empty node
   name, no action
   to take

========================================================================================================================

   takes the             +-----------+
   pod out               |           |
       +---------------->+ Scheduler +--------+
       |                 |           |        |
       |                 +-----------+        | schedules
       |                                      | the pod
       |                                      |
       |                                      |
       |                                      v
+------+-------+                          +---+--+
|              |                          |      |
|  scheduling  |                          | Node |
|  queue       |                          |      |
|              |                          +------+
+--------------+

========================================================================================================================

+-----------+
|           |
|  kubelet  |
|           |
+-----+-----+
      |
      | notices the change
      | and updates the pod
      |
      v
+-----+-----+
|           |
| Informer  |
|           |
+-----------+

========================================================================================================================

+--------------+
|              |        no more replicas
|  ReplicaSet  +----->  to create,
|  controller  |        nothing to do
|              |
+-------+------+
        |
        | notices change
        | in pod
        |
        v
 +------+-----+
 |            |
 |  Informer  |
 |            |
 +------------+

========================================================================================================================

 pod terminates
      +
      |
      |
      |
      |
      |
      v
+-----+----+          +-----------+         +-----------------+
|          | notices  |           | updates |                 |
| Informer +<---------+  kubelet  +-------->+ API server/etcd |
|          |          |           |         |                 |
+----------+          +-----------+         +-----------------+

========================================================================================================================

 pod terminated
      +
      |
      |
      v
+-----+------+
|            |
|  Informer  |
|            |
+-----+------+
      ^
      |notices change
      |in pod
      |
+-----+------+                 +-------------------+
|            |                 |                   |
| ReplicaSet +---------------->+  API Server/etcd  |
| controller |     deletes     |                   |
|            |  terminated pod |                   |
+-----+------+                 +-------------------+
      |
      |
      |
      |
      |
      v
  creates new
  pod to replace
  deleted one

```

### Optimistic concurrency

Write operations can fail, especially in distributed systems with concurrent write operations. The Kubernetes API server
implements a strategy called _optimistic concurrency_ to deal with concurrent writes. In a simple manner, it means that
when and if concurrent writes are detected, the API server will reject the latter of the two write operations. The
client that initiated the request must then handle the failure. Some pseudocode to illustrate this idea:
```
while(retryCount < 10) {
    resource = getResourceFromAPIServer();

    // operation to update the resource and/or the world

    error = updateResource(resource);
    if(error) {
        retryCount++;
    } else {
        break;
    }
}
```

In other words, developers must expect errors to arise from write operations and handle them gracefully when
implementing Kubernetes-native applications.

## Operators

Operators are particular cases of controllers. They are application-specific controllers that extend the Kubernetes API
to create, configure and manage instances of complex stateful applications on behalf of a Kubernetes user. Operators
include domain or application-specific knowledge to automate common tasks.

Three conditions must hold for operators:
1. There's some domain-specific knowledge that you wish to automate.
1. The best practices for this operational knowledge are known and can be made explicit.
1. The artifacts shipped in the context of the operator are:
    - A set of _custom resource definitions_ (CRDs) capturing the domain-specific schema and custom resources that
    represent the domain of interest.
    - A custom controller that supervises the custom resources.
