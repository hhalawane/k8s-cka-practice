# Get pods and image name
``kubectl describe pods``

# Deploy pod with label
`kubectl create PODNAME --image=IMAGENAME --labels tier=ieks`

# Create Pod and run on container port
kubectl create custom-nginx --image=nginx --port=80

# Create a Service and assign clusterIP
`kubectl create service clusterip httpd --tcp=`

# Create service and expose
`kubectl expose pod redis --port=6379 --name=redis-service`
`kubectl run httpd --image=httpd:alpine --port=80 --expose`

# Scale replicas
`kubectl scale --replicas=4 rs/replicaset`

# Run image
`kubectl run NAME --image=IMAGE `

# Describe Namespace etc
describe current NS - `kubectl config current-context`
switch NS - `kubectl config use-context <NAMESPACE>`
describe pods namespaces - `kubectl get pods --all-namespaces`

**Deploy a Pod**
`kubectl run nginx --image=nginx`

**Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)**
`kubectl run nginx --image=nginx --dry-run=client -o yaml`

**Create a deployment**
`kubectl create deployment --image=nginx nginx`

**Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)**
`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml`

**Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run) and save it to a file.**
`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml`

**Make necessary changes to the file (for example, adding more replicas) and then create the deployment.**
`kubectl create -f nginx-deployment.yaml`

## When creating a new pod - use `kubectl run` command!
`kubectl run --image=redis:alpine redis -l tier=db`
`kubectl run custom-nginx --image=nginx --port=8080`

## Use `kubectl create` when defining replicas
`kubectl create deployment redis-deploy --replicas=2 --namespace=dev-ns --image=redis`

# Imperative Commands in Kubernetes

## Create Objects
- `kubectl run --image=nginx nginx`
- `kubectl create deployment --image=nginx nginx`
- `kubectl expose deployment nginx --port 80`

## Update Objects
- `kubectl edit deployment nginx`
- `kubectl scale deployment nginx --replicas=5`
- `kubectl set image deployment nginx nginx=nginx=1.18`

- `kubectl create -f file.yaml`
- `kubectl delete -f file.yaml`
- `kubectl replace -f file.yaml`
- `kubectl replace --force -f file.yaml`

# Declarative Commands in Kubernetes
Declarative uses YAML files for creating deployments
## Create Objects
- `kubectl apply -f file.yaml`
- `kubectl apply -f /path/to/config-files`

## Update Objects
- `kubectl apply -f file.yaml`

# Schedule pod to specific node
See spec.nodeName in image below, just change node01 for specific node for use![](Pasted%20image%2020250409135809.png)
To see all pods use command `kubectl get all`

If you were asked to create a pod or deployment with specific name and image you can simply run the `kubectl run` command.


# Assign taint to node
`kubectl taint nodes node01 spray=mortein:NoSchedule`

See doc: https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/

![[Pasted image 20250409144030.png]]

![](attachments/Pasted%20image%2020250409144030.png)


**How many objects are in the `prod` environment including PODs, ReplicaSets and any other objects?** `kubectl get all --selector env=prod`




