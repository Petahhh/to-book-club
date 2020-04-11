# Section 5 - Observability

## Readiness Probes

Pods have a status and conditions

status = where in their lifecyle they are in
{pending > container creating > running (> terminated)}


condition = set of true and false values for different states of a pod
{PodScheduled (true or false), Initialized (true or false), ContainersReady, Ready}
* you can look this up using `kubectl describe pod` and scroll down the "Conditions"

### Ready vs ContainersReady Condition

* ContainersReady: The container is running and the processes has _started_
* Ready: the pod is ready to accept traffic because the process is _running_ and is able to perform it's responsibilities 
  * this state can be set by creating *probes*
  
### Probes

* probes are used to inform the `Ready` state
* they can be setup by providing endpoints like
  * `/api/ready`
  * healthcheck
  * configuring `spec.readinessProbe` in your pod definition

Thesepod definition defines a probe that k8s will use to determine the `Ready` state of the pod. 

#### `readinessProbe` for http endpoint

```
...
kind: Pod
spec:
  ...
  readinessProbe:
    httpGet:
      path: /api/ready
      port: 8080
```


#### `readinessProbe` for tcp endpoint

```
...
kind: Pod
spec:
  ...
  readinessProbe:
    tcpSocket:
      port: 3306
```


#### `readinessProbe` for an executable file

```
...
kind: Pod
spec:
  ...
  readinessProbe:
    exec:
      command:
        - cat
        - /app/is_ready
```

### `readinessProbe` options

* `readinessProbe.initialDelaySeconds` can be set to offset when probing begins
* `readinessProbe.periodSeconds` can be set to configure how often the probe occurs
* `readinessProbe.failureThreshold` can be used to override the default of `3` attempts probes are made

## Liveness Probes `spec.readinessProbe`

* sometimes containers can be up and running but do to a bug (e.g. infinite loop) it's not properly handling traffic even though the container is running
  * to fix this, it may require the pod to be destroyed and recreated
* liveness probes can be set to periodically check the health of the application
  * unhealthy pods are automatically recreated
* it has the same structure as readinessProbe 

## Container Logging

`docker logs -f <container id>` allows you to tail the logs of a running container

`kubectl logs -f <pod name>` allows you to do the same for a running pod

For multiple containers within a pod you must specify the container name e.g.

`kubectl logs -f <pod-name> <container-name>`


