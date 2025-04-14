# Selectors & Labels

**Get pods using multiple labels**
`kubectl get pods -l environment=production,tier=frontend`

**Apply label to node**
`kubectl label nodes <your-node-name> disktype=ssd`

# Taints and Node Affinity

**Remove taint from node with kubectl**
First describe the node to find Taint - `kubectl describe node <NodeName>`
and then use command below:
`kubectl taint node <NodeName> node-role.kubernetes.io/master:NoSchedule-`

**Deploy with nodeAffinity**
`kubectl create deployment <deploymentname> --image=imagename --replicas=2 `

# Edit pod memory limit
- first create a file from a pod
	`kubectl get pod PODNAME -o yaml > elephant.yaml`

- Then replace with newly created file
	`kubectl replace -f elephant.yaml --force`

# Quick note on editing pods and deployments

## Edit Pods

### First Option
- Run command `kubectl edit pod my-pod` and edit the deployment
- Run command `kubectl replace --force -f /tmp/kubectl-edit-mypod.yaml` - use the file that is temporary saved
### Second Option
**The second option is to extract the pod definition in YAML format to a file using the command**
`kubectl get pod webapp -o yaml > my-new-pod.yaml`

**Then make the changes to the exported file using an editor (vi editor). Save the changes**
`vi my-new-pod.yaml`

**Then delete the existing pod**
`kubectl delete pod webapp`

**Then create a new pod with the edited file**
`kubectl create -f my-new-pod.yaml`

## Edit Deployments

With Deployments you can easily edit any field/property of the POD template. Since the pod template is a child of the deployment specification,  with every change the deployment will automatically delete and create a new pod with the new changes. So if you are asked to edit a property of a POD part of a deployment you may do that simply by running the command

`kubectl edit deployment my-deployment`

# Daemon Sets
Used for monitoring and logging nodes.

![](Pasted%20image%2020250409223243.png)

An easy way to create a DaemonSet is to first generate a YAML file for a Deployment with the command 
`kubectl create deployment elasticsearch --image=registry.k8s.io/fluentd-elasticsearch:1.20 -n kube-system --dry-run=client -o yaml > fluentd.yaml`. 

Next, **remove** the **replicas**, **strategy** and **status** fields from the YAML file using a text editor. Also, change the kind from `Deployment` to `DaemonSet`.  
  
Finally, create the **Daemonset** by running `kubectl create -f fluentd.yaml`

![](Pasted%20image%2020250409221042.png)

# Multiple Schedulers
Use the default scheduler or multiple schedulers to assign pods to the right nodes
with using custom schedulers.

![](Pasted%20image%2020250410111507.png)
**Custom Scheduler usage in YAML file** ![](Pasted%20image%2020250410113317.png)
```
Multiple schedulers betekent gewoon dat je **meer dan één scheduler** kunt draaien in je cluster. Elk van die schedulers kan zijn **eigen logica** hebben om te bepalen waar Pods terecht moeten komen.
```

To check if scheduler is assigned to the right pod use commands:
- Get events - `kubectl get events -o wide`
- View scheduler logs - `kubectl logs PODNAME/DEPLOYNAME --name-space=NAMESPACE`

Create configMap from File
```
kubectl create configmap NAMECONFIGMAP --from-file=place/of/file.yaml -n NAMESPACE
```

# Scheduler Profiles
Scheduler profiles in Kubernetes zijn essentieel een manier om verschillende configuraties te hebben voor hoe pods worden toegewezen aan nodes in je cluster.

Normaal gesproken heeft Kubernetes één scheduler die beslist waar nieuwe workloads draaien. Met scheduler profiles kun je meerdere configuraties hebben voor deze scheduler, elk met eigen regels en prioriteiten.

# Admission Controllers
> Ze zijn onderdeel van je **governance en policy-laag**, net als RBAC en netwerk policies.

Denk aan Admission Controllers als de “compliance agent” in je cluster:

- ✅ _"Alle Pods moeten CPU-limits hebben."_
- ✅ _"Niemand mag containers met `:latest` tag gebruiken."_
- ✅ _"Alle resources moeten een label `team=` hebben."_

Zonder admission controllers kun je dit soort dingen **niet afdwingen**. Dan ben je afhankelijk van best effort of dev-discipline. En dat wil je niet in een productieplatform.
![Pasted image 20250410142002.png](Pasted%20image%2020250410142002.png)
> Kube-apiserver yaml file is in: /etc/kubernetes/manifests

# Webhooks

## Wat is een _webhook server_ in Kubernetes?
Een **webhook server** is een stukje code dat door Kubernetes wordt aangeroepen op bepaalde momenten om **extra logica** toe te voegen. Denk aan:

- iets controleren (**validatie**),
- iets aanpassen (**mutatie**),
- of iets weigeren/goedkeuren voordat het in je cluster komt.

## ⚙️ Hoe werkt het technisch in Kubernetes?
1. 🧠 Jij maakt een **webhook server**. Dit is een API die luistert naar HTTP-verzoeken (meestal met TLS).
2. 📦 Je maakt een **Deployment** en **Service** in je cluster voor die server.
3. 🔗 Je maakt een **`MutatingWebhookConfiguration`** of **`ValidatingWebhookConfiguration`** resource in Kubernetes.  
    Deze zegt:
    > “Als iemand een Pod aanmaakt, stuur de request ook naar _mijn webhook_.”
4. 🧩 Kubernetes stuurt die request naar jouw server. De server controleert of past aan.
5. 🛠️ Kubernetes gebruikt het antwoord om te bepalen of het object wordt doorgelaten, aangepast of geweigerd.

## 🎯 Soorten webhooks

|Type|Wat doet het?|
|---|---|
|Mutating|Past resources aan vóórdat ze worden opgeslagen.|
|Validating|Keurt resources goed of af vóórdat ze worden opgeslagen.|
> Webhook servers draaien **bij voorkeur met TLS** (HTTPS), en moeten **snel reageren** (timeout ≈ 10s), anders vertraag je de hele Kubernetes API.

