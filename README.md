# Provisionning a k8s cluster Lab with Vagrant, VirtualBox, and Ansible

This project sets up a simple Kubernetes cluster on local virtual machines using Vagrant, VirtualBox, and Ansible. It consists of:

- A **Control Node**: used to run Terraform/Ansible for automation.
- A **Kubernetes Cluster**: one control-plane (master) node and two worker nodes.

---

## Prerequisites

Make sure the following tools are installed on your host machine:

- [Vagrant](https://www.vagrantup.com/) 
- [VirtualBox](https://www.virtualbox.org/)  
- [VisualCode](https://code.visualstudio.com/download)

Visual Code terminals are the simpliest way to manage this project in order to be able to ssh to different VMs created via vagrant.

---

## Project Structure

```
.
├── Vagrantfile               # Vagrantfile for provisioning the control node
├── k8s-cluster/
│   ├── Vagrantfile           # Vagrantfile for provisioning Kubernetes cluster VMs
│   ├── playbooks/
│   │   ├── common.yaml       # Common setup: containerd, kubeadm, etc.
│   │   ├── kernel-settings.yaml  # Enable required kernel modules
│   │   ├── init-master.yaml  # Init the Kubernetes control plane
│   │   ├── join-workers.yaml # Join the worker nodes to the cluster
│   └── inventory/
│       └── hosts.ini         # Ansible inventory file
```

---

## Step-by-Step Guide

###  Provision the Control Node (Admin VM)

In the root of the repository:

```bash
cd /prov-k8s-cluster
vagrant up
```

This brings up a VM that acts as your "admin" or "control node", with ansible, terraform, kubectl and git installed. It will generate a public rsa key as well to help you will use this machine to manage the Kubernetes cluster with Ansible.

You can login into it:

```bash
vagrant ssh control-node
```
To logout and shutdown the VM:
```bash
exit #or crtl + d to exit the ssh session
vagrant halt
```

---

### 2 Provision the Kubernetes Cluster Nodes

In the `k8s-cluster` directory:

```bash
cd k8s-cluster
vagrant up
```

This creates 3 VMs: 1 master and 2 workers. You have one challenge there, think of it or you will find it out in the next step

---

### Configure the Cluster with Ansible

From within the **control node**:

Login to the control node:

```bash
vagrant ssh control-node
```
Clone the repisotry to access the needed playbooks:

```bash
git clone https://github.com/Omar-Bensalah/prov-k8s-cluster.git
```
Run the following playbooks and enjoy the challenge (Previously asked)
```bash
cd ~/k8s-lab/terraform/k8s-cluster
ansible-playbook -i inventory/hosts.ini playbooks/kernel-settings.yaml
ansible-playbook -i inventory/hosts.ini playbooks/common.yaml
ansible-playbook -i inventory/hosts.ini playbooks/init-master.yaml
ansible-playbook -i inventory/hosts.ini playbooks/join-workers.yaml
```

The playbooks will ensure installing all the requirements to have a locally configured cluster. Think of installing manually your preferred CNI, or add a playbook and don't hesitate to raise a merge request!!

---

### Verifying the cluster:

SSH the k8s control plane (can be solved with the challenge mentionned peviously):

```bash
vagrant ssh control-plane
kubectl get nodes
```
### Next challenge:

- Provisionning an EKS cluster using terraform
- Updating Common playbook to automate the installation of CNI plugin, Metrics and Kubernetes dashboard.

