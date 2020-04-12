# Section 6 - Pod design

## Labels, selectors and annotations

* labels are key value pairs you attach to objects
* selectors are used to filter objects
* sometimes ReplicaSet and Pods have the same labels which can be confusing because it looks redundant - but since ReplicaSetse are Pods are both objects, it's still sensible for both to have labels that may be the same
  * having a ReplicaSets' and Pods' labels matching means the pods that are created are "part of" the ReplicaSet

### Annotations

`metadata.annotations` are key value pairs you can add to objects for additional information to be stored
