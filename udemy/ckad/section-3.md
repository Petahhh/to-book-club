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


## Docker Security

* hosts see a docker container's processes and pids
* a container will see it's pid as `1` and doesn't know about the processes on the host. The host will see this pid but it won't be `1` - it'll be another number. The `1` the container sees is the pid with respect to it's namespace
* `docker run --user=1000` can specify which user the process runs as or using `USER` in the Dockerfile
* the `root` user within the container is not the same as the `root` user on the host - it is limited by linux capabilities
* by default docker ensures the process within it's container is very limited
  * use `docker run --cap-add` to add capabilities
  * if YOLO `docker run --priveleged` the process can do errthing

## Security Contexts

* security settings can be carried at a pod level and apply to all the containers in the pod
  * if there are pod and container level security settings, the container settings override the pod settings

* pod wide security contexts can be set in pod definitions with `spec.securityContext`
```
...
spec:
  securityContext:
    runAsUser: 1000
```

* you can move `securityContext` to the container template to make that security context container specific

```
...
spec:
  containers:
    - name: ubuntu
      ...
      securityContext:
        runAsUser: 1000
        capabilities: # capabilities can be container specific but not pod specific
          add: ["MAC_ADMIN"]
```

## Service Accounts

Service Accounts (by bots: prometheus to monitor, jenkins to deploy) vs User Accounts (by humans: admins/devs)

`kubectl create serviceaccount <your-service-account>` # creates a serviceaccount

* service account tokens are generated automatically with each serviceaccount to be used by the service
  * a secret object is created to store the token
  * the secret object is linked to to the serviceaccount

`kubectl describe serviceaccount <your-service-account>` # shows you the serviceaccount and name of secret token is in
`kubectl describe secret <generated-secret>` # shows you the token

Token can be used as the token bearer for making rest calls to the k8s api

Shortcut - If the app you're running needs a k8s api token, you can mount the secret as a volume on the pod

* a `default` serviceaccount is created for each namespace
* pods within the namespace automatically receive the serviceaccount and token mounted as a volume


### Attaching a Service Account to a Pod via it's definition

* you can attach service accounts to a pod via the pod definition

`pod-definition.yml`
```
...
spec:
  ...
  serviceAccount: new-service-account
```

### Pod Recreation Fun Fact

* we know changing Pod definition requires a delete and recreate for many properties
* by using a deployment, changes in the pod _template_ mean the deployment controller knows to destroy and recreate the pod for you
* you can use this technique to swap out serviceAccount keys easily

### Disable automatic service account mounting in a pod

* you can disable the automated mounting of the default service account to a pod through `spec.automountServiceAccountToken = false`

`pod-definition.yml`
```
...
spec:
  ...
  automountServiceAccountToken: false

```