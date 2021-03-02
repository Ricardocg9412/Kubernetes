# Replicasets 

El objeto de un ReplicaSet es el de mantener un conjunto estable de réplicas de Pods ejecutándose en todo momento. Así, se usa en numerosas ocasiones para garantizar la disponibilidad de un número específico de Pods idénticos

- Replication controller : Help us provide HA with multi pods in a single node  
- Load balancing and Scaling : multiple nodes 

- Replica set : newest approach 


## kubectl get replicaset
## kubectl delete replicaset 
- Update number of replicas/ Scale 
## kubectl replace -f .yml 
## kubectl scale --replicas=n -f yml

```bash
apiVersion: apps/v1
kind: ReplicaSet
metadata: #List
   name: myapp-replicaset
   labels:
        app: myapp
        type: front
spec:
  template: 

    metadata: 
      name: myapp-pod
      labels:
        app: myapp
        type: front
    sppec:
      containers: # Array
      - name: cont1
        image: python:3.6-alpine
  replicas: 3 # numero de pods
  selector:
     matchLabels:
        type: front  # Agrega otros pods que no se crren aqui  solo que hagan mathc con el label 
```
