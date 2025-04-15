# OS Upgrades

- Empty pods before upgrades ``
```shell
kubectl drain NODENAME
```

- Make Node unschedulable aka SchedulingDisabled
```shell
kubectl uncordon NODENAME
```

# Cluster Upgrades with kubeadm
*Always upgrade the control-plane node first and then the worker nodes!*

> Documentation: https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/
> 
> *Check the Debian package for the right version to upgrade - might need changes*

## Upgrade Control-Plane
```shell

# Update the kubeadm
sudo apt-mark unhold kubeadm && \
sudo apt-get update && sudo apt-get install -y kubeadm='1.32.0-1.1' && \
sudo apt-mark hold kubeadm

# Verify version
kubeadm version

# Verify upgrade plan
sudo kubeadm upgrade plan
sudo kubeadm upgrade apply v1.x.x

# Upgrading kubelet on controlplane
kubectl drain controlplane --ignore-daemonsets
sudo apt-mark unhold kubelet kubectl && \
sudo apt-get update && sudo apt-get install -y kubelet='1.32.0-1.1' kubectl='1.32.0-1.1' && \
sudo apt-mark hold kubelet kubectl

# Restart services
sudo systemctl daemon-reload
sudo systemctl restart kubelet

# Bring node back online - Uncordon the node
kubectl uncordon controlplane

# REPEAT SAME STEPS FOR MORE CONTROLPLANES
```

## Upgrade Worker Nodes - Linux Nodes

> **SSH into the worker node first!** - `ssh node1`

- Upgrade kubeadm on Worker Node
```shell
# replace x in 1.32.x-* with the latest patch version
sudo apt-mark unhold kubeadm && \
sudo apt-get update && sudo apt-get install -y kubeadm='1.32.x-*' && \
sudo apt-mark hold kubeadm

# Apply upgrade
sudo kubeadm upgrade node

# Drain the node -  to prepare node for maintenance
kubectl drain NODENAME --ignore-daemonsets

# Upgrade kubelet and kubectl
sudo apt-mark unhold kubelet kubectl && \
sudo apt-get update && sudo apt-get install -y kubelet='1.32.0-1.1' kubectl='1.32.0-1.1' && \
sudo apt-mark hold kubelet kubectl

# Restart Services
sudo systemctl daemon-reload
sudo systemctl restart kubelet

# Uncordon node to bring back online
kubectl uncordon NODENAME

# REPEAT SAME STEPS IF NEEDED FOR MORE NODES TO UPGRADE
```

# Backup and Restore Methods
## Commands that come in handy
- Get IP address of external etcd datastore in cluster2
```shell
ssh cluster2-controlplane ps -ef | grep --color=auto etcd
```
- Check how ETCD is configured in a cluster
```shell
# Make sure to be in the right cluster, use command
kubectl config use-context CLUSTERNAME

# We have two options to find how ETCD is configured
kubectl get pods -n kube-system

# Or describe the pod and find --etcd-servers
kubectl describe pod kube-apiserver-cluster1-controlplane -n kube-system
```


## Backup ETCD
- The master node in our cluster is planned for a regular maintenance reboot tonight. While we do not anticipate anything to go wrong, we are required to take the necessary backups. Take a snapshot of the **ETCD** database using the built-in **snapshot** functionality. 
  Store the backup file at location `/opt/snapshot-pre-boot.db`
```shell
# Run the commands:
export ETCDCTL_API=3 
etcdctl --endpoints=https://[127.0.0.1]:2379 \ 
--cacert=/etc/kubernetes/pki/etcd/ca.crt \ 
--cert=/etc/kubernetes/pki/etcd/server.crt \ 
--key=/etc/kubernetes/pki/etcd/server.key \ 
snapshot save /opt/snapshot-pre-boot.db
```

- Restore to original state
```
```


**ETCD stores information about the state of the cluster**

Since our ETCD database is TLS-Enabled, the following options are mandatory:

`--cacert`                                                verify certificates of TLS-enabled secure servers using this CA bundle

`--cert`                                                    identify secure client using this TLS certificate file

`--endpoints=[127.0.0.1:2379]`          This is the default as ETCD is running on master node and exposed on localhost 2379.

`--key`                                                      identify secure client using this TLS key file

## Restore ETCD



