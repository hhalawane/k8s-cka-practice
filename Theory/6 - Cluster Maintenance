# OS Upgrades

- Empty pods before upgrades ``
```
kubectl drain NODENAME
```

- Make Node unschedulable aka SchedulingDisabled
```
kubectl uncordon NODENAME
```

# Cluster Upgrades
Documentation: https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/

- Upgrade Cluster with kubeadm
```
kubeadm upgrade plan
kubeadm upgrade apply
```

- Upgrade to version of choice with kubeadm
```
kubeadm upgrade -y kubeadm=1.12.0-00
kubeadm upgrade apply v1.12.0

# Upgrade Kubelet after 
apt-get upgrade -y kubelet=1.12.0-00
systemctl restart kubelet

# Verify Upgrade
kubectl get nodes
```

- Upgrade node - repeat steps for multiple nodes
```
# Drain the node
kubectl drain node-1

# upgrade the node after drain
kubeadm upgrade -y kubeadm=1.12.0-00
apt-get upgrade -y kubelet=1.12.0-00
kubeadm upgrade node config --kubelet-version v1.12.0
systemctl restart kubelet

# Make node scheduleable after upgrade
kubectl uncordon node-1
```


# Backup and Restore Methods


# Backup and Restore Methods 2
