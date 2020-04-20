# Section 7 - Services and Networking

## Services

* microservices can be deployed in several sets of pods - services allow communication between the sets of pods and users to access the app
* k8s services is an object that listens to a port on the node and forwards the request to the pod (this is known as a node port service)

### Service Types

NodePort - makes internal pods accessible on the node

Cluster IP - makes a virtual ip within the cluster which enables inter-cluster communication

LoadBalancer - distributes traffic between nodes

### NodePort

There are 3 ports to consider with NodePort services
* Target port: the service forwards requests to this port on the POD
* "The Port": refers to the port of the service
  * the service is like a virtual server in the node
  * within the cluster the service has a "cluster ip"
* NodePort: the port on the node that the service listens from
  * the valid range of NodePorts is 30000-32767

`service-definition.yml`
```
apiVersion: v1 
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports: # multiple port mappings allowed
    - targetPort: 80
      port: 80
      nodePort:30008
  selector:
    app: myapp
    type: front-end
```

* if targetPort is not specified, it defaults to the same as port
  * if port is not specified than it is randomly assigned a valid port
* use selector to "bind" them to pods (use the same labels as the pod you want to expose)
  * if multiple pods match the selector, traffic will be forwarded to anyt of them
    * randomly algorithm used to load balance traffic    
* if pods are spread out across nodes, the service object spans all the nodes automatically


### Cluster IP

* pod IPs are not static and cannot be relied on - pods are killed and recreated regularly
* a cluster ip service can be used to group pods as "backend", "database" or "frontend" to help specify what communication you want enabled even while pods go down and come back up

`cluster-ip-def.yml`
```
apiVersion: v1
kind: Service
metadata:
  name: backend
spec:
  type: ClusterIP (this is the default type for services)
  ports:
  - targetPort: 80 (where "backend" is listening)
    port: 80 (where the service will be listening)
  selector:
    app: myapp
```
This will allow pods to access the service under a single "cluster ip" (literally an ip that will always work) or by service name
