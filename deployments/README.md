## Deployments

- A Kubernetes deployment is a resource object in Kubernetes that provides declarative updates to applications. A deployment allows you to describe an application's life cycle, such as which images to use for the app, the number of pods there should be, and the way in which they should be updated.

- Puedes definir Deployments para crear nuevos ReplicaSets

# kubectl get all 
# kubectl get deployments 
# kubectl get replicasets
# kubectl get pods 

## kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml