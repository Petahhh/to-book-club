# Section 6 - Pod design

## Labels, selectors and annotations

* labels are key value pairs you attach to objects
* selectors are used to filter objects
* sometimes ReplicaSet and Pods have the same labels which can be confusing because it looks redundant - but since ReplicaSetse are Pods are both objects, it's still sensible for both to have labels that may be the same
  * having a ReplicaSets' and Pods' labels matching means the pods that are created are "part of" the ReplicaSet

### Annotations

`metadata.annotations` are key value pairs you can add to objects for additional information to be stored

## Rolling Updates and Rollbacks in Deployments

* deployments have "Revision" number `kubectl rollout status deployment/<deployment-name>`
* Rollouts cause revision number to increase
* to see the rollout history run `kubectl rollout history deployment/<deployment-name>`
* for your deployment you can specify a deployment strategy
  * `recreate` kills all existing pods and then creates new ones
  * `rolling update` (the default), kills and creates pods one at a time

`kubectl apply -f deployment-definition.yml` will redeploy your deployment with all the changes in the yml

`kubecetl set image deployment/<dep name> nginx=nginx:1.9.1` will redeploy with a change in image
  * note that this deployment now has a different definition file now that you do not have a copy of

`kubectl describe deployment <dep name>` will tell you the deployment information

### Upgrades under the hood

* Under the hood a new replica set is created
* new pods are being created on the new replica set
  * simultaneously pods in the original replica set are being taken down in a rolling upgrade fashion

### Rollback

* rolling back to a previous revision:
   * `kubectl rollout undo deployment/<deployment-name>` will destroy pods in the new replica set and bring back pods in the original one
   
`kubectl rollout status deployment/<deployment-name>` shows you the status of the rollout

`kubectl rollout ... --record` adding this flag for edit/undo/set image will record the command that triggered the change. This will then show up in `kubectl rollout history deployment/<deployment-name>` "Change-Cause" column

## Jobs

* there are different kinds of workloads:
  * a task that runs and finishes (computation like machine learning computing an answer)
  * a service that stays up indefinitely (database, web app)
  
### `spec.restartPolicy` is "always" by default

* this means if you want to run a one time computation, even when it finishes k8s will recreate the container right afterwards resulting in a cycle of running and stopping endlessly needlessly
* setting `spec.restartPolicy` to:
  * `Never` it runs only once
  * `OnFailure` so that it only restarts when the job fails
  
### Distributing workloads on k8s with Job

* Sometimes you want to run many workloads not continuously but want to ensure these workloads run and finish successfully. This can be done by creating a Job

job-definition.yml
```
apiVersion: bathc/v1
kind: Job
metadata: 
  name: math-job
spec:
  template:
    <pod spec with spec.restartPolicy set to Never>
```

* K8s jobs run the container until it is successfully completed
* the standard output of the container (accessible from `kubectl logs`) contains the result of the job
* the job's `spec.completions` can be set so that k8s ensures there is at least that many number of successful runs of the pod
  * Pods are created sequentially until the completions is met
* `spec.parallelism` can be set to control the number of pods created in parallel
  * k8s is smart enough to only spin up subsequent pods in parallel to meet `spec.completions` count
    * e.g. if paralleism = 2, completions = 3, then k8s will spin up 2 pods and if they both succeed, will spin up 1 additional pod to reach the target of 3

## Cron Jobs

K8s can run jobs based on a cron schedule. This object looks like this:

```
apiVersion: bathc/v1beta1
kind: CronJob
metadata:
  name: your-cron-job
spec:
  schedule: "*/1 * * * " # your cron schedule
  jobTemplate:
    <job spec>
```

Note that there are going to be 3 specs:
  * Cron Job spec which contains the jobTemplate
  * Job spec which contains the template (the pod template)
  * the pod spec
