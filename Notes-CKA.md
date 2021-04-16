## Certified Kubernetes Administrator (CKA) - Notes

# Comandos 

- kubectl run bee --image=nginx --dry-run -o yaml bee.yaml   // Correr Pod y generar archivo
- kubectl create deployment blue --image=nginx --replicas=3  // Crear deployment 
- kubectl get pod webapp -o yaml > my-new-pod.yaml  // Extract pod definition in YAML file 
- kubectl run --restart=Never --image=busybox static-busybox --dry-run=client -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yam // Static pod 

# SCHEDULING 

- Manual scheduling 

```bash
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: node01  -- Manual put the name of the node 
  containers:
  -  image: nginx
     name: nginx
```

- Pod binding definition 

```bash
apiVersion: v1
kind: Binding
metadata:
  name: nginx
target:
  apiversion: v1
  kind: Node
  name: node01
```

# Labels and Selectors

- Labbels

- #kubectl label nodes NODE_NAME KEY:VALUE

- Selectors 
### kubectl get pods --selector app=App

- Annotations :  Puedes usar las anotaciones de Kubernetes para adjuntar metadatos arbitrarios a los objetos, de tal forma que clientes como herramientas y librerías puedan obtener fácilmente dichos metadatos.

# Taints and tolerations 

- Taints are set on nodes and Tolerations on pods 

- ej: kubectl taint nodes NODE-NAME key=VALUE:TAINT EFECT   {NoSchedule | PreferNoSchedule | NoExecute}
- kubectl taint nodes node1 app=blue:NoSchedule

- Tolerations: 

```bash
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  -  image: nginx
     name: nginx

  tolerations: 
    key: "app"
    operator: "equal"
    value: "blue"
    effect: "NoSchedule"
```

- get taint on a node ## kubectl describe node node01 | grep -i taint

- Remove A taint 
- #kubectl taint nodes master/controlplane node-role.kubernetes.io/master:NoSchedule-

# Node Selectors 

```bash
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  -  image: nginx
     name: nginx
  nodeSelector: 
     size: Large
```

# Node Affinity 

- preferredDuringSchedulingIgnoredDuringExecution
- requiredDuringSchedulingIgnoredDuringExecution

- Any pod will continue working on the asigned pod even if the label is removed 
- *There is a planed where it should be removed 

```bash
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution: # 3 type of values 
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ss
```

# Resource Limits and Requirements 

- Resource requests: minimum amount 

```bash
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:
      memory: 512Mi
    defaultRequest:
      memory: 256Mi
    type: Container
```

# Daemon Sets 

- DaemonSets are used to ensure that some or all of your K8S nodes run a copy of a pod, which allows you to run a daemon on every node. When you add a new node to the cluster, a pod gets added to match the nodes. Similarly, when you remove a node from your cluster, the pod is put into the trash.

- kubectl get daemonsets --all-namespace

# Static pods 

- Static pods are a type of pod that is not observed or managed via kube-apiserver and is directly bound to the Kubelet daemon on the node.

- kubectl run --restart=Never --image=busybox static-busybox --dry-run=client -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yam  || Crear static pod 

# Multiple Schedulers 

- Deploy aditional scheduler 
1. Download kube-scheduler 
- Create file for new scheulder 

2. Deploy with kubeadm as a pod 
- Create definition file as in /etc/kubernetes/manifests/kube-scheduler.yaml 

```bash
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: node01  -- Manual put the name of the node 
  containers:
  -  image: nginx
     name: nginx
```

- Add shceduler name in the pod definition 

```bash
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  -  image: nginx
     name: nginx
  schedulerName: my-custom-scheduler 
```

- #kubectl get events 
- #kubectl logs my-custom-scheduler --name-space=kube-system