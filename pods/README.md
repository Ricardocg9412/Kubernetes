## PODS Documentation 

- Los pods son los objetos más pequeños y básicos que se pueden implementar en Kubernetes. Un pod representa una instancia única de un proceso en ejecución en tu clúster. Los pods contienen uno o más contenedores, como los contenedores de Docker.

- kubectl run nginx --image=nginx
- kubectk get pods 
- kubectl describe pod
- kubectl delete pod 
- kubectl edit pod 

## Basic syntax yml file 

```bash
apiVersion: v1
kind: Pod
metadata: #List
   name: podtest1
   labels:
        app: example
spec:
   containers: # Array
   - name: cont1
     image: python:3.6-alpine
```