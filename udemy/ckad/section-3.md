# Section 3 - Configuration

## Commands and Args in Docker

* `docker run ubuntu` # starts a ubuntu image but exits right away
  * a container only lives as long as the process inside it is alive
    * `CMD` in your `.dockerfile` defines this process
* `docker run ubuntu sleep 5` # creates an override for the process on the container
* `CMD` is followed by an array of strings which is used as the command run on the container
  e.g. `CMD ["sleep", "5"]` results in `$ sleep 5` being run
* `ENTRYPOINT` can specified which will be used as the executable for the container. This allows you to parametrize running the container

Given `Dockerfile`
```
FROM Ubuntu
ENTRYPOINT ["sleep"]
# CMD can be omited
```
When you `docker build -t ubuntu-sleeper . && docker run ubuntu-sleeper 10`
Then the image will run `sleep` with the argument `10`

* the `docker` flag `--entrypoint` can be used to override the `ENTRYPOINT` in the Dockerfile/Image

## POD Commands

* you can specify docker args with your pod definition

e.g.

`pod-definition.yml`
```
...
spec:
  containers:
    - name: ubuntu-sleeper
      image: ubuntu-sleeper
      args: ["10"]
```

This results in `docker run ubuntu-sleeper 10` within the pod

* you can also specify a entrypoint override in your pod-definition with `spec.containers[x].command`

e.g. 
`pod-definition-with-entrypoint-override.yml`
```
...
spec:
  containers:
    - name: ubuntu-sleeper
      image: ubuntu-sleeper
      command: ["entrypointExecutable"] # the entrypoint "sleep" is overriden with "entrypointExecutable"
      args: ["10"]
```

Summary:

|In your pod definition, this|overrides the Dockerfile value:|
|---|---|
|`spec.containers[x].command`|`ENTRYPOINT`|
|`spec.contaienrs[x].args`|`CMD`|


## Editing PODs/Deployments

* `kubectl edit deployment your-deployment` can edit any field property of the pod template
  * changes in the deployment results in pods being redeployed
  
* `kubectl edit pod` is limited to the following spec values:
  * `spec.containers[*].image`
  * `spec.initContainers[*].image`
  * `spec.activeDeadlineSeconds`
  * `spec.tolerations`
  * to change other specs you'll have to recreate (download definition, update it, delete and recreate)
