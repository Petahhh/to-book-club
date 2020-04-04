# Section 4 - Multi Conatiner Pods

## Multi-Container Pods

* in a microservices world sometimes you want to pair your application with a "side car" process
  * an agent that is distinct but coupled with your application that you want:
    * to turn on/off in sync with the app
    * communicate with each other or share resources (disk space)
    * e.g. logger
* multi-containers can be achieved in the pod definition by specifying another container in the `spec.containers` array

## Multi-Container Patterns

### Sidecar

* e.g. logger agent

### Adapter

* e.g. something that can convert logs into a unified format to be consumed elsewhere

### Ambassador

* say you're communicating to a db - you can break up that logic into another process
* this process can be a separate container in your pod and be configured different depending on the purpose of that pod e.g. a dev instance might have the db process pointed to a sqlite while a production instance would point to a real database

