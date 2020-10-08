# Testing environment

Tested on a system setup using this [vagrantfile](misc/vagrant/Vagrantfile).    
Kubernetes installed using kubespray with [inventory](misc/kubespray/inventory.yaml) and    
this [deploy command](misc/kubespray/runKubespray.sh)

# Instructions

## Set Contrail release version
```
RELEASE=R2008
```
## Create Operator, roles, bindings and crds
```
kubectl apply -k \
  github.com/michaelhenkel/contrailkustomize/operator/${RELEASE}
```
## Wait for the CRDs to be created
```
kubectl wait crds --for=condition=Established --timeout=2m managers.contrail.juniper.net
```
## Create Contrail
### Set scale to 1 or 3 node(s)
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
## get password for UI
User name is 'admin', password can be retrieved by
```
kubectl -n contrail get secret cluster1-admin-password -ojson |jq .data.password | tr -d '"' |base64 --decode
```
## Cleanup
```
kubectl delete -k github.com/michaelhenkel/contrailkustomize/contrail/${REPLICA}node/${RELEASE}
kubectl delete -k github.com/michaelhenkel/contrailkustomize/operator/R2008
kubectl delete crds --all
kubectl delete pv --all
```
also, remove /mnt/zookeeper and /mnt/cassandra from the master nodes    
