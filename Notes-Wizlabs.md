# Installation Configuration & Validation

## Kubernetes Cluster

- Single and multi node clusters 
- Cluster components 

    - Master Node :
    etcd -> Configuration data 
    kube-apiserver -> Frontend for control plane 
    kube-scheduler -> Schedule pods
    kube-controller-manager -> Runs multiple controllers 
        - Node Controller
        - Replication controller 
        - Endpoints controller 
        - SA and Token controllers

    - Worker Node: 
    kubelet - cluster agent 
    kube-proxy  - network communication
    Container Runtime 

## Design Kubernetes cluster 

- Componentes and number of replicas
5000 nodes
150000 pods
300000 containers
100 pods per node 

- Resource requirements 
- Number of worker nodes 
- Size of masters 
https://kubernetes.io/docs/setup/best-practices/cluster-large/
- internal or external etcd server
- High Availability 
- Cluster monitoring 
metrics-server 
- Infra 
- Security 

## K8s infra configuration 

- docker - container runtime 
- kubeadm - to install kubernetes 
- minimum 2 CPU 2 GB RAM 
- network connectivity required 
- KOPS  supported for EKS 
- KRIB - for bare metal servers 
- Kubespray  

- kubelet - node component, agent 
- kubectl CLI utility 
version should match with k8s version 

## Use kubeadm to install kubernetes on ec2 instance 

## Network 

- coredns to get ready 

## Install worker nodes 
- kubeadm join control-plane-host:control-plane-port --token --discovery-token-ca-cert-hash
- kubernetes dashboard 
https://github.com/kubernetes/dashboard

## End to end tests
- kubectl cluster-info 
- kubectl cluster-info dump 
- kubectl get nodes -o wide
- kubectl get events 
- kubectl logs 
- node conformance test 

# Core Concepts 

## Kubernetes Cluster Arch

- Management of nodes 
nodes created separately and added to the cluster 
kubectl get nodes 

- Node controller
assign CIDR to node 
keep list of nodes up to date 
monitor health nodes

- Controller pattern 
built in and resillient controllers 

## Kubernetes API Primitives 
Restful via HTTP 
Everyhtins is threated as API object 
- Workloads
deplyments, statefulsets, etc 

- Discovery and Load Balancer 
Servicess and ingress

- Config anf Storage
ConfigMaps, secrets and volumes 

- Cluster resources 
cluster roles, role bindings and cerrtificates 

- Metadata 
HPA, 

SCOPING 
1. Cluster scoped 
2. Namespace scoped 

API versioning 
alpha, beta , stable 

API Groups 
batch/v1 

## Kubernetes Objects 
Represent the desired state of the cluster 
YAML 
- Spec: descirbes the desired state provided by the user 
- Status: actual state 

Pods: 
Single or multiple containers 
shared storage volumes 
share network namespace
ephemeral entities
metadata 
pod states: 
    pending
    running 
    succeded 
    failed 
    unknown

container states: 
    waiting
    running
    terminated

- Imperative commands 
kubectl run
kubectl create 

- Imperatice oBECJ coNFIG 
KUBECTL CREATE -F 
        DELETE 

- Declarative Object Conifg
- Names should be unique within namespace 
- Nmaespaces
default: objects without namespace
kube-system: objects created by k8s system 
kube-public: cluster usage
kube-node-lease: lease objects 

kubectl connfig 

## Labels, Selectors and Annotations 
- labels: key value pairs attached to the object 
are optional 

- selectors: indetify a set of objects  = == =! 
1. equality based : match all of the specified llabels 
2. set.based : in, nothin, exists 

- Annotations: Attach arbitrary metadata to an object 

## Services and network primitives 

Provides an abstraction for a set of pods and policy to access 
Types:
1. ClusterIp - Exposes the service within the cluster only 
maps incoming port to a targetPort
2. NodepORT - Expossed on each Nodes Ip 
ClusterIp service auto created 
accessible foprm external at NodeIp:NodePort
3. LoadBlancer - Expose the service using cloud providers LB 
NodePort and ClusterIP auto created
4. ExternalName - uses DNS names instead selectors 

Multi port pods 
headless service - none for ClusterIP

# NETWORKING 

## Networking configutation on cluster nodes 
every pod gets unique IP 
- container to container communication 
contianers within a pod share network namespaces 
- pod to pod commmunication
pODS CAN COMMUNICATE WITHOUT Procies
- pod to service 
kube-procy programs iptanles and redirects to backends 
- external to internal 
NodepORT, LoadBalancer

Network models: 
1. Calico 
2. Flannel 

## Pod networking concepts

## Services Networking 
DNS Service 
kubernetes control plane allocates ports from 30000-32767 range 
- LB
aws by defualt uses CLB 
for NLB need to add an annotation 
for internal LB also need to add annotation 

- Ingress and Ingress Controllers
             