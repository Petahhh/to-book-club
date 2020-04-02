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

## Environment variables

* in a pod definition you can specify an array `spec.containers[*].env` environment variables

`pod-definiton.yml`
```
...
spec:
  containers:
    - name: someThing
      .
      .
      .
      env:
        - name: APP_CONFIG_VALUE
          value: 7
```
is equivalent to `docker run -e APP_CONFIG_VALUE=7`

* alternatively you can use `valueFrom` instead of `value`
e.g.
```
.
.
.

env:
  - name: APP_CONFIG_VALUE
    valueFrom:
      configMapKeyRef:
  - name: OTHER_CONFIG
    valueFrom:
      secretKeyRef:
```

## ConfigMaps

* key-value pair to stay value which can be injected into a pod

`kubectl create configmap myconfigmap --from-literal=APP_CONFIG=7` # creating it imperatively


`kubectl create configmap myconfigmap --from-file=config.properties` # declaritively

`config.properties`
```
APP_CONFIG: 7
```

or 

`kubectl create -f config-map.yml`

`config-map.yml`
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: myconfigmap
data:
  APP_CONFIG: 7
```

`kubectl get configmaps` # lists config maps in `default` namespace
`kubectl describe configmap` # lists config maps with the data/key-value pairs

### ConfigMaps in Pods

* you can use the `spec.containers[*].envFrom` key to specify which configmap to use for the pod

```
.
.
.
spec:
  containers:
  - name: some-app
    .
    .
    .
    envFrom:
      - configMapRef:
          name: myconfigmap
```

### Specify a single environment variable from a configmap

```
.
.
.
env:
  - name: APP_CONFIG
    valueFrom:
      configMapKeyRef:
        name: myconfigmap
        key: APP_CONFIG
```

### Using Volumes

```
.
.
.
volumes:
- name: app-config-vol
  configMap:
    name: app-config
```

## Secrets

* passwords can be placed in config maps but they are still in plain text

### Creating a Secret
`kubectl create secret generic shhhh --from-literal=password=hehe` # imperative

`kubectl create secret generic shhhh --from-file=app.secrets`

`app.secrets`
```
HOST: mysql.address
PASSWORD: password123
USER: you
```

`kubectl create -f secret-definition.yml` # declaritively
`secret-definition.yml`
```
apiVersion: v1
kind: Secret
metadata:
  name: shhh
data:
  HOST: mysqladdresss
  USER: password123
  PASSWORD: password123
```


`echo -n "some-string" | base64` # encodes the string - you should store the encoded passwords in the secret definition

`echo -n "encoded-string@#$_342342 | base64 --decode` # to decode


### Using secrets in pod definitions

#### Using an entire secret

`pod-definition.yml`
```
.
.
.
spec:
  containers:
  - name: your-app
    .
    .
    .
    envFrom:
      - secretRef:
        name: shhh
```

The above injects all the secrets in `shhh` as environment variable


#### Using a specific value within a secret

To inject a individual secrets from a secret:

```
.
.
.
env:
  - name: DB_PASSWORD
    valueFrom:
      secreteKeyRef:
        name: shhh
        key: DB_PASSWORD_IN_shhh
```

#### Using volumes for secrets

```
...
volumes:
- name: app-secret-volume
  secret:
    secretName: app-secret
```

This results in volumes on the container:

```
$ ls /opt/app-secret-volumes
> DB_HOST DBPASSWORD DB_USER
$ cat /top/app-secret-volumes/DBPASSWORD
> password123
```
