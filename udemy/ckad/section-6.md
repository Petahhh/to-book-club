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
