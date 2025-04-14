# Create Secrets

## Create Secret from File
> Edit and create secret file, `kubectl create -f secret-file.yaml`

```secret-data.yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
data:
  DB_Host: mysql 
  DB_User: root
  DB_Password: paswrd
```

## Create Secret from CLI
```
kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123
```

## Use secretRef in Pod YAML File

>
> **Place envFrom below container information!**

```
---
apiVersion: v1 
kind: Pod 
metadata:
  labels:
    name: webapp-pod
  name: webapp-pod
  namespace: default 
spec:
  containers:
  - image: kodekloud/simple-webapp-mysql
    imagePullPolicy: Always
    name: webapp
    envFrom:
    - secretRef:
        name: db-secret
```

# Edit Pods with kubectl edit
- Edit the pod with command `kubectl edit pod PODNAME`
- save with `wq!` and do it once again with `q!`
- Force the replace of edit in file with command, `kubectl replace --force -f /tmp/kubectl-edit.yaml`

# Multi Container PODs
```
---
apiVersion: v1
kind: Pod
metadata:
  name: app
  namespace: elastic-stack
  labels:
    name: app
spec:
  containers:
  - name: app
    image: kodekloud/event-simulator
    volumeMounts:
    - mountPath: /log
      name: log-volume

  - name: sidecar
    image: kodekloud/filebeat-configured
    volumeMounts:
    - mountPath: /var/log/event-simulator/
      name: log-volume

  volumes:
  - name: log-volume
    hostPath:
      # directory location on host
      path: /var/log/webapp
      # this field is optional
      type: DirectoryOrCreate
```

# Init Containers
At times you may want to run a process that runs to completion in a container. For example a process that pulls a code or binary from a repository that will be used by the main web application. That is a task that will be run only  one time when the pod is first created. Or a process that waits  for an external service or database to be up before the actual application starts. 
That's where **initContainers** comes in.

An **initContainer** is configured in a pod like all other containers, except that it is specified inside a `initContainers` section,  like this:

```
apiVersion: v1
kind: Pod
metadata:
   name: myapp-pod
   labels:
     app: myapp
 spec:
   containers:
   - name: myapp-container
     image: busybox:1.28
     command: ['sh', '-c', 'echo The app is running! && sleep 3600']
   initContainers:
   - name: init-myservice
     image: busybox
     command: ['sh', '-c', 'git clone <some-repository-that-will-be-used-by-application> ; done;']
```

When a POD is first created the initContainer is run, and the process in the initContainer must run to a completion before the real container hosting the application starts.

## Multiple Init Containers
You can configure multiple such initContainers as well, like how we did for multi-containers pod. In that case each init container is run **one at a time in sequential order**.

If any of the initContainers fail to complete, Kubernetes restarts the Pod repeatedly until the Init Container succeeds.

```
apiVersion: v1
kind: Pod
metadata: 
name: myapp-pod
labels: 
app: myapp
spec: 
containers:
  - name: myapp-container
image: busybox:1.28
command:
  - sh
  - -c
  - echo The app is running! && sleep 3600
initContainers:
  - name: init-myservice
    image: busybox:1.28
    command:
      - ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
  - name: init-mydb
    image: busybox:1.28
    command:
      - ['sh', '-c', 'until nslookup mydb; do echo waiting for mydb; sleep 2; done;']
```

# Autoscaling in Kubernetes

## Vertical Scaling
Vertical Scaling is adding more resources to the application, think of adding more CPU or Memory
## Horizontal Scaling
Horizontal Scaling is adding more Instances/Servers known as Replicas in Kubernetes

## Scaling in Kubernetes
You can scale manual or automated in K8S

Scaling in K8S goes in two options:
- **Scaling Cluster Infra**: 
	- **Manual**: Horizontal Scaling `kubeadm join`
	- **Automated**: Cluster Autoscaler
- **Scaling Workloads**: 
	- **Manual**: Horizontal Scaling `kubectl scale`, Vertical Scaling `kubectl edit`
	- **Automated**: Horizontal Pod Autoscaler (HPA), Vertical Pod Autoscaler (VPA)

**Commands for Scaling** 
- Scaling Workloads manual
```
kubectl scale deploy DEPLOYNAME --replicas=3
```

- Autoscale with command and review if Autoscale (HPA) works
```
kubectl autoscale deploy DEPLOYNAME ---cpu-percent=50 --min-1 --max=10
kubectl get hpa
```

- Example HPA Definition File 
```my-app-hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

- Scaling K8S Vertical Manual - **VPA is not built-in with K8S, you must deploy it**
`kubectl edit deployment DEPLOYNAME` and edit requests.cpu to a random number

- Example VPA Definition File
```
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: my-app-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind:       Deployment
    name:       my-app
  updatePolicy:
    updateMode: "Auto"
  resourcePolicy:
    containerPolicies:
    - containerName: "my-app"
      minAllowed:
        cpu: "250m"
      maxAllowed:
        cpu: "2"
      controlledResources: ["cpu"]
```

## Install VPA on /root directory
- Clone repo: `git clone https://github.com/kubernetes/autoscaler.git`
- Go to Directory `cd autoscaler/vertical-pod-autoscaler`
- Run script: `./hack/vpa-up.sh`

- Check Status VPA 
```
kubectl get pods -n kube-system | grep vpa
```

- Monitor Pod Resource Usage (see image for example)
```
kubectl top pod
```
![](attachments/Pasted%20image%2020250414141612.png)

## Check Resources VPA
1. **Use the `kubectl get` command** to identify the recommended target CPU value:

```bash
   kubectl get vpa flask-app -o yaml
```

- Look for the `Recommendation` section under the `Status` field.
- Identify the `target.cpu` value for the relevant container (e.g., `flask-app-4`).

1. **Manually note** the target CPU value and store it in `/root/target`.

**Example Output**
```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  annotations:
  name: flask-app
  namespace: default
spec:
  resourcePolicy:
    containerPolicies:
    - containerName: '*'
      controlledResources:
      - cpu
      maxAllowed:
        cpu: 1000m
      minAllowed:
        cpu: 100m
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: flask-app-4
  updatePolicy:
    updateMode: "Off"
status:
  conditions:
  - lastTransitionTime: "2024-11-07T06:18:17Z"
    status: "True"
    type: RecommendationProvided
  recommendation:
    containerRecommendations:
    - containerName: flask-app-4
      lowerBound:
        cpu: 100m
      target:
        cpu: 143m
      uncappedTarget:
        cpu: 143m
      upperBound:
        cpu: "1"
```

3. Store the target CPU value in `/root/target`:

```
echo "143m" > /root/target
```