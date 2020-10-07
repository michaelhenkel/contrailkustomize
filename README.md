# Instructions

## Set release
```
RELEASE=R2008
```
## Create roles, bindings, operator and crds
```
kubectl apply -k \
  github.com/michaelhenkel/contrailkustomize/operator/${RELEASE}
```
## Create Contrail
### set scale to 1 or 3 node
```
REPLICA=1
```
or    
```
REPLICA=3
```
### Deploy Contrail
```
kubectl apply -k \
  github.com/michaelhenkel/contrailkustomize/contrail/${REPLICA}node/${RELEASE}
```
## Cleanup
