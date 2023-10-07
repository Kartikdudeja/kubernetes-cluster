# Production-like Multi-Node Kubernetes Cluster Deployment with Vagrant and Ansible

This project provides a set of configurations to deploy a Production-like Multi-Node Kubernetes cluster on your local machine using Vagrant and Ansible. With this setup, you can quickly create a Kubernetes environment for development, testing, or learning purposes.

## Prerequisites

Before getting started, make sure you have the following prerequisites installed on your system:

1. [Vagrant](https://www.vagrantup.com/downloads)
2. [VirtualBox](https://www.virtualbox.org/wiki/Downloads) (or another supported Vagrant provider)

## Setup Overview

We will be setting up a Kubernetes cluster that will consist of one master and two worker nodes. All the nodes will run Ubuntu Bionic 18.04 LTS 64-bit OS and Ansible playbooks will be used for provisioning. Master Node also acts as the Ansible Controller Node, while provisioning Master Node will install ansible as well and will be executing the ansible playbooks to setup and configure k8s master node and install helm, then configure worker nodes as well.

## Getting Started

Follow these steps to deploy the Kubernetes cluster:

1. Clone this repository to your local machine:

   ```bash
   git clone https://github.com/Kartikdudeja/k8s-cluster.git
   ```

2. Change into the project directory:

   ```bash
   cd k8s-cluster
   ```

3. Customize the cluster configuration (if needed):

    You can customize the cluster configuration:

     - Number of worker nodes
     - Virtual machine resources (CPU and memory)
     - Network settings

    You can customize the number of Worker Nodes, Resources for the VMs in the `Vagrantfile`.  
    Incase you change IP or increase number of worker nodes, update the `inventory.ini` file and necessary IP updates in the `master-playbook.yaml` file.

5. Start the Vagrant virtual machines:
   ```bash
   vagrant up
   ```

This command will start up the Virtual Machines, and the provisioning block in the Master Node will install the necessary software, and it will run the Ansible Playbooks as well.
Ansible will configure the Kubernetes cluster on the provisioned virtual machines.

5. Access and verify the Kubernetes cluster:
   ``` bash
   $ vagrant ssh k8s-master
   vagrant@k8s-master:~$ kubectl get nodes -o wide
   NAME         STATUS   ROLES                  AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION       CONTAINER-RUNTIME
   k8s-master   Ready    control-plane,master   30m   v1.28.2   10.10.10.100   <none>        Ubuntu 18.04.6 LTS   4.15.0-206-generic   containerd://1.6.21
   k8s-node-1   Ready    worker                 24m   v1.28.2   10.10.10.101   <none>        Ubuntu 18.04.6 LTS   4.15.0-206-generic   containerd://1.6.21
   k8s-node-2   Ready    worker                 24m   v1.28.2   10.10.10.102   <none>        Ubuntu 18.04.6 LTS   4.15.0-206-generic   containerd://1.6.21  
   ```

## Cleaning Up

To destroy the Kubernetes cluster and virtual machines, run the following command:

```bash
vagrant destroy -f
```

## Contributing

Contributions to this project are welcome! If you have ideas for improvements or encounter issues, please open an issue on the GitHub repository.

## Acknowledgments

This project is built upon the work of many contributors in the Kubernetes, Vagrant, and Ansible communities. Special thanks to the authors of the original project that inspired this one.

   
