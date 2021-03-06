# UPGRADE KUBERNETES CLUSTER

Below are the steps we need to perform for upgrading the **Kubernetes** cluster.

Note: This document is only from the exam point of view and for upgrading production based clusters, we need to take more points into consideration.


Get the detailed information about the upgrade process from the below-mentioned website.

[Kubernetes](https://kubernetes.io)

 

## UPGRADE CONTROLPLANE NODE


```bash

# Update the repositiry

sudo apt update



# Check for the lastest kubernetes release versions

sudo apt-cache madison kubeadm

```

**UPGRADE KUBEADM**

 
```bash

# Unholds to upgrade kubeadm package

sudo apt-mark unhold kubeadm


# Install the new version of kubeadm, here the version is 1.20.4-00

sudo apt update && sudo apt install -y kubeadm=1.20.4-00

 
# Hold the package to prevent accidential upgrade

sudo apt-mark hold kubeadm

 
# Fetches the control plane component versions to which we can update to.

sudo kubeadm upgrade plan

 
# Upgrade kubeadm

sudo kubeadm upgrade apply v1.20.4

```

 

**UPGRADE KUBECTL AND KUBELET**
 

```bash

# Drain the control plane node

kubectl drain manager --ignore-daemonsets

 

# Unholds to upgrade for kubelet and kubectl

sudo apt-mark unhold kubelet kubectl

 

# Install the new version of kubelet and kubectl, here the version is 1.20.4-00

sudo apt-get update && sudo apt-get install -y kubelet=1.20.4-00 kubectl=1.20.4-00

 

# Hold the package to prevent accidential upgrade

sudo apt-mark hold kubelet kubectl

 

# Will reloads the systemd manager configuration

sudo systemctl daemon-reload

 

# Will restart the kubelet service

sudo systemctl restart kubelet

 

# Check the status of the service

sudo systemctl status kubelet

 

# Uncordon the cntrol plane node.

kubectl uncordon manager

```

 

## UPGRADE WORKER NODES

Note: worker nodes should not be upgraded simultaneously.

 
**UPGRADE KUBEADM**

 
> On Worker node

```bash

# Update the repositiry

sudo apt update

 

# Unholds to upgrade kubeadm

sudo apt-mark unhold kubeadm

 

# Install the new version of kubeadm, here the version is 1.20.4-00

sudo apt update && sudo apt install -y kubeadm=1.20.4-00

 

# Hold the package to prevent upgrade

sudo apt-mark hold kubeadm

 

# Upgrade the local configuration

sudo kubeadm upgrade node

```

 

**UPGRADE KUBECTL AND KUBELET**

 
> On control plane node

```bash

# Drain the worker1 node

kubectl drain worker1 --ignore-daemonsets --delete-emptydir-data

```

 

> On Worker node

```bash

# Unholds to upgrade kubelet and kubectl

sudo apt-mark unhold kubelet kubectl

 

# Install the new version of kubelet and kubectl, here the version is 1.20.4-00

sudo apt-get update && sudo apt-get install -y kubelet=1.20.4-00 kubectl=1.20.4-00

 

# Hold the package to prevent upgrade

sudo apt-mark hold kubelet kubectl

 

# Will reload the systemd manager configuration

sudo systemctl daemon-reload

 

# Will restart the kubelet service

sudo systemctl restart kubelet

```

 

> On control plane node

```bash

# Uncordon the worker node to bring it online and run the below command on control plane node.

kubectl uncordon worker1

```

 

Perform above steps on all the worker nodes to upgrade kubeadm, kubelet and kubectl.

 

> On control plane node

```bash

# Verify the cluster to list the updated nodes.

kubectl get nodes

```


