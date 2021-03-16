# Namespaces

default
kube-public       
kube-system

- kubectl get pods --namespace=NAME // get pods of a specific namespace
- kubectl create -f --namespace=NAME // create pod in a specific NS
- kubectl create namespace NAME 
- kubectl get pods --all-namespaces  // list all pods 
- kubectl config set-context $(kubectl config current context) --namespace=NAME // swith bewteen namespaces

- Use resource quota to limit resources in a namespace 

## Basic syntax yml file 

```bash
apiVersion: v1
kind: Namespace
metadata: #List
   name: dev
```