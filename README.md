# Prerequisites

Tested on a k8s installation deployed with Kubespray using the following inventory    
```
cat kubespray/inventory/mycluster/inventory.yaml 
all:
  hosts:
    server1.local:
      ansible_host: 172.26.208.101
      ansible_become: yes
    server2.local:
      ansible_host: 172.26.208.102
      ansible_become: yes
    server3.local:
      ansible_host: 172.26.208.103
      ansible_become: yes
    server4.local:
      ansible_host: 172.26.208.104
      ansible_become: yes
  vars:
    kube_version: v1.18.0
    kubectl_localhost: true
    kubeconfig_localhost: true
kube-master:
  hosts:
    server1.local:
    server2.local:
    server3.local:
kube-node:
  hosts:
    server4.local:
  vars:
    node_labels:
      node-role.opencontrail.org: vrouter
etcd:
  hosts:
    server1.local:
    server2.local:
    server3.local:
k8s-cluster:
  children:
    kube-master:
    kube-node:
```
using the following deploy command    
```
ansible-playbook -i inventory/mycluster/inventory.yaml cluster.yml -e "kube_network_plugin=cni" -e "kube_network_plugin_multus=false" -b -v
```
# Instructions

## Set release
```
RELEASE=R2008
```
## Create Operator
### Create roles, bindings, operator and crds
```
kubectl apply -k \
  github.com/michaelhenkel/contrailkustomize/operator/${RELEASE}
```
### Wait for the CRDs to be created
```
kubectl wait crds --for=condition=Established --timeout=2m managers.contrail.juniper.net
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
```
kubectl delete -k github.com/michaelhenkel/contrailkustomize/contrail/${REPLICA}node/${RELEASE}
kubectl delete -k github.com/michaelhenkel/contrailkustomize/operator/R2008
kubectl delete crds --all
kubectl delete pv --all
```
also, remove /mnt/zookeeper and /mnt/cassandra from the master nodes    
