# CURSO KUBERNETES

## Pre requisitos
- Instalar kubectl 
- Instalar minikube

## Comandos:
  minikube start --vm-driver=none
  minikube status 
  minikube dashboard

  kubectl run --generator=run-pod/v1 podtest --image=nginx:alpine 
  kubectl get pods
  kubectl get pods  --watch     #ver logs 
  kubectl describe pod $pod
  kubectl delete pod $pod
  kubectl get pod $pod -o yaml 
  kubectl exec -it $pod (-c $contenedor) -- sh 
  kubectl logs $pod
  kubectl api-versions
  kubectl api-resources   #lista de objetos de kubernetes
  kubectl apply -f YAML FILE
  *(python -m http.server 8081)
  kubectl get pods -l app=dev   //Filtrar pod por label 
  kubectl explain node // explain nodes 
  kubectl logs $DEPLOYMENT // VIEW LOGS 

 ## REPLICASETS
 kubectl get replicaset
 kubectl label pods $pod label=label     //label a pod 

 ## DEPLOYMENTS
 kubectl get deployment
 kubectl get deployment --show-labels
 kubectl rollout status deployment $deployment
 - Deployment crea en auto un replica set 
 - Roll updates en deployment
 kubectl describe deployments.apps $deployment
 kubectl rollout history deployment $deployment     //historico de cambios 
 -- change cause
 kubectl apply -f .yaml --record
 --rollout
 kubectl rollout undo deployment $deployment
 kubectl create deployment NAME --image=IMAGE

 ## SERVICES (Balanceador entrega un IP unica)
 - 2 tipos de puertos
 - Type por default es ClusterIP 
 - endpoints == IP de pods 
 - se crea en automatico un DNS con el nombre del servicio 
 
 # Tipos de servicio:
 - ClusterIP - Comunicacion interna
 - NodePort - Exponer el servicio 
 - LoadBalancer
 
 kubectl get services
 kubectl describe service $service
 kubectl get endpoints
 
 ## DOCKERIZE AN app
 - write code 
 - create dockerfile copying code 
 - create docker image from dockerfile #docker build -t NOMBRE -f Dockerfile . 
 - create docker container with image built #docker run -d -p PUERTO --name  IMAGEN 
 - Publicar imagen en docker hub # docker tag y docker push 
 - Crear manifiest en kubernetes para generear el deployment y servicio 
 - Exponer aplicacion front y back 
 
 ## NAMESPACE (Isolated spaces)
 kubectl get pods --namespace default 
 kubectl get namespaces
 -default
 -kube-system   # Datos de kubernetes
 kubectl create namespace NOMBRE
 kubectl get all -n NAMESPACE
 kubectl describe NOMBRE
 
 dns en namespace == servicio.namespace.svc.cluster.local 
 ej..  http://backend-k8s.ci.cluster.local
 
 Contexto 
 kubectl config current-context   ## Obtiene la configurtacion de conexto actual en el archivo .kube/config
 kubectl config view

kubectl run --rm -ti --generator=run-pod/va podtest --image=nginx:alpine -- sh  # Correr pod temporal 
kubectl config set-context ci-context --namespace=ci --cluster=minikube --user=minikube  # crear nuevo contexto 
kubectl config use-context ci-context  # cambiar de contexto

## LIMITS Y REQUESTS 
- lIMIT == LIMITE DE MEMORIA
- REQUEST == capacidad DE MEMORIA 

kubectl get nodes
kubectl describe node minikube   ## allocation de cpu 

qos pods == tipo de pod de acuerdo a su configuracion 
(guaranteed / burstable / BestEffort)
kubectl get pods $POD -o yaml | grep -i qos

## LIMITRANGE  (A nivel de namespace Y OBJETO)
Inyecta los valores al ns y todos los pods creados dentro obtendran esos valores
kubectl get limitrange 
kubectl describe limit range NOMBRE -n
kubectl get pod $POD -o yaml -n dev | grep -i limits / requests 
kubectl describe ns 

## RESOURCE QUOTA (A NIVEL DE NAMESPACE EN GENERAL LIMITA TODOS LOS RECURSOS)
kubectl describe resourcequotas -n dev NOMBRE


## PROBES (Diagnosticos a pods como health check  comando/puerto/ HTTP request)
- Livenes :  Health check 
- Readines :  Diagnostico antes de exponer el pod 
- Startup :  checar que la app este arriba 

## VARIABLES DE ENTORNO 
- env: 
- name: VAR1
  value: "valor de prueba 1"

  env > dentor del pod nos deja ver las variables de ENTORNO
  kubectl exec -ti envar-demo -- sh > entar al pod 

  --variables de referencia 
  kubectl get pods -o yaml  > ver datos del pod 

## CONFIG MAPS  (CONFIGURACIONES GLOBALES) key - value configs (data no sensible)
kubectl create configmap nginx-config --from-file=configmaps-examples/nginx.conf
kubectl get configmaps
kubectl describe configmaps nginx-config

- 2 formas de atachar el config map 
  1. a traves de un volument montado en el pod 
  2. a traves de variables de entoron 

## SECRETS  (data sensible)
kubectl create secret generic mysecret --from-file=./secret-files/test.txt
kubectl get secrets
kubectl describe secrets mysecret
kubectl delete secret mysecret
-Los archivos son decodeados en base64
echo c2VjcmV0MT1ob2xhCnNlY3JldDI9YWRpb3M= | base64 --decode

stringData lo encodea a base 64 \ data tienes que poner el valor en base 64 

## VOLUMENES (Persistent data)
- EmptyDir (crear directorio a nivel del pod | muere con el pod )
- HostPath (no depende del pod, depende del nodo)
- CloudVols (EBS y Persistent Disks)

- Persistent Volume ( encargado de aprovisionar cloud storage)
- Persistent Volume Claim ( Reclama espacio al PV)

- Reclaim ( Retain (No se elimina el PV pero ya no es accesible )
         | Recycle (Elimina contenido del PV y lo deja disponible ) 
         | Delete (Se elimina todo alv))

kubectl get pv
kubectl descrive $PV
kubectl get pvc

- Storage class (aprovisionamiento dinamico)
kubectl get sc


## RBAC USERS AND GROUPS 
- Role == define namespace vs ClusterRole == no define namespace
- RoleBinding = asignar rol a usuario mediante archivo yaml
- ClusterRoleBinding  =
- Usuarios = Crear certificados para cada usuario (kubernetes no tiene herramienta para crear users)
-

-Crear cert 
kubectl config view   =cert del cluster 
# certificate-authority: /Users/josepinon/.minikube/ca.crt

kubectl cluster-info dump | grep authorization-mode   = verificar que RBAC este habilitado

-Roles 
kubectl get roles
kubectl desribe role pod-reader -n NAMESPACE
kubectl get rolebinding

-ClusterRole == ACCESO A TODOS LOS RECURSOS DEL Cluster
kubectl get clusterroles
administrador == cluster-admin

## Service Account  (pods permissions) (RoleBinding asignado a service account)
-1 por defualt al que son asignados los pods 
kubectl get serviceaccount
kubectl get secret

- Hacer llamados a la api de kubernetes desde un pod dentro del cluster 
curl http://Kubernetes/api/v1/namespaces/default/pods --insecure

- Utilizando token para hacer request 
TOKEN = $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
curl -H "Authorization: Bearer ${TOKEN}" https://10.96.0.1/api/v1 --insecure
- Se deben agregar los roles al sa para que tenga permisos adecuados

## INGRESS (Exponer servicios fuera del cluster)
- Cluster IP 
- Node Port (puerto arriba de #3000)
- Load Balancer (Cloud)

- Ingress controller 
Deployment que aplica las reglas para dirigir trafico en el cluster con Ingress 
Nginx o GCP 
1. Crear controldor nginx-controller.yaml
----*** OBTENER IP DEL SERVICIO EN MINKUBE ***------
  minikube service ingress-nginx --url -n ingress-nginx

2. Crear servicio y app ingress-example.yaml
3. Crear reglas ingress-rules.yaml
4. Para trabajar con dominio agregar dominio a /etc/hosts
192.168.64.2 app1.mydomain.com
5. Crear segunda aplicacion para exponerla en 9090 app-ingress2.yaml
6. Agregar nuevas reglas a rules.
7. Reglas para cada host y path ingress-rules-host.yaml


### AWS EKS
- Instalar AWS CLI 
- Crear y configurar usuario con acceso de admin en IAM y CLI 
 aws sts get-caller-identity  -- comprobar usuario 
- Instalar eksctl (like kubectl)
 eksctl version 

- Crear cluster en EKS
#eksctl create cluster --name test-cluster --without-nodegroup --region us-east-1 --zones us-east-1a,us-east-1b
#aws eks --region us-east-1 update-kubeconfig --name test-cluster  ///Archivo de configuracion 
#kubectl get svc 
#kubectl cluster-info   // info del cluster 

- Crear pod
#kubectl run --generator=run-pod/v1 test --image=nginx:alpine    // No se genera porque hay que crear el nodo
#kubectl get nodes   // no hay 

- Crear Nodos
#eksctl create nodegroup \
--cluster test-cluster \
--version auto \
--name test-workers \
--node-type t3.medium \
--node-ami auto \
--nodes 1 \
--nodes-min 1 \
--nodes-max 3 \
--asg-access

#kubectl get nodes   // se crea instancia de EC2 
#kubectl delete pod test 

### Ingress en AWS 
- IAM prerequisites 
eksctl utils associate-iam-oidc-provider \
--region us-east-1 \
--cluster test-cluster \
--approve

#aws iam create-policy \
    --policy-name ALBIngressControllerIAMPolicy \
    --policy-document https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/iam-policy.json

eksctl create iamserviceaccount \
    --region us-east-1 \
    --name alb-ingress-controller \
    --namespace kube-system \
    --cluster test-cluster \
    --attach-policy-arn arn:aws:iam::043185404238:policy/ALBIngressControllerIAMPolicy \
    --override-existing-serviceaccounts \
    --approve

#kubectl get all -n 2048-game 

***Pasos detallados para creal ALB 
https://docs.aws.amazon.com/eks/latest/userguide/alb-ingress.html

#kubectl port-forward pod/2048-deployment-dd74cc68d-cvtqj -n 2048-game 7000:80 ///Ver pod local desde un cluster externo 

- Despues de crear el servicio 
#kubectl get ingress -n 2048-game  // obtener DNS || Desde aws buscar en load balancers 
// El listener forwardea a un target group 

- Editar reglas del ingress 
#kubectl edit ingress 2048-ingress 0n 2048-ingress 

// Agregar la regla 

spec:
  rules:
  - host: app.aws.game
    http:
      paths:
      - backend:
          serviceName: service-2048
          servicePort: 80
        path: /*

- Crear DNS local que apunte a la ip del DNS de AWS 
 nslookup d7f12bb1-2048game-2048ingr-6fa0-815062298.us-east-1.elb.amazonaws.com // Obtener ip y agregarla a /etc/hosts


### Horizontal Pod Scaler (Escala pods de acuerdo al uso de CPU basado en metricas)

- Inastalar metric server 
https://docs.aws.amazon.com/eks/latest/userguide/horizontal-pod-autoscaler.html

- Crear HPA 
kubectl run httpd --image=httpd --limits=cpu=500m --expose --port=80  // Crear deployment para testear 
kubectl autoscale deployment httpd --cpu-percent=50 --min=1 --max=10  //Crear HPA 
kubectl describe hpa httpd 
kubectl hpa edit hpa httpd 


##Cluster autoscaler (Ademas de pod scala los nodos)

https://docs.aws.amazon.com/eks/latest/userguide/cluster-autoscaler.html

















