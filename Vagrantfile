# Vagrantfile for setting up the Kubernetes Lab

# base box image for VMs
IMAGE_NAME="bento/ubuntu-18.04"

# number of worker node in the k8s cluster
WORKER_NODES=2

# worker nodes resources
NODE_CPUS=1                                 # cpu core count assigned to VM
NODE_MEMORY=1024                            # memory (in GB)

# master node resources
MASTER_CPUS=2
MASTER_MEMORY=2048

Vagrant.configure("2") do |config|

    # manages the /etc/hosts file on guest machines in multi-machine environments
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true

    # common configuration for the hypervisor
    config.vm.provider "virtualbox" do |vb|
        # fix for slow network speed issue
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end

    ### Worker Node Configuration ###
    (1..WORKER_NODES).each do |i|
        config.vm.define "k8s-node-#{i}" do |node|
    
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "10.10.10.#{i + 100}"
            node.vm.hostname = "k8s-node-#{i}"

            node.vm.provider "virtualbox" do |vb|
                vb.name = "k8s-node-#{i}"
                vb.memory = NODE_MEMORY
                vb.cpus = NODE_CPUS
            end

        end

    end

    ### Master Node Configuraton ###
    config.vm.define "k8s-master" do |master|
    
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "10.10.10.100"
        master.vm.hostname = "k8s-master"

        master.vm.provider "virtualbox" do |vb|
            vb.name = "k8s-master"
            vb.memory = MASTER_MEMORY
            vb.cpus = MASTER_CPUS
        end

        # Provisioning Kubernetes Cluster:
        master.vm.provision "shell", inline: <<-SHELL
            
            # install ansible
            echo | sudo apt-add-repository ppa:ansible/ansible
            sudo apt update
            sudo apt install ansible -y

            sleep 15

            # configure master node in k8s cluster
            ansible-playbook -i /vagrant/k8s-setup/inventory.ini /vagrant/k8s-setup/master-playbook.yaml

            sleep 30

            # configure worker node in k8s cluster
            ansible-playbook -i /vagrant/k8s-setup/inventory.ini /vagrant/k8s-setup/worker-node-playbook.yaml

            sleep 30

            # add lables to k8s nodes
            sudo kubectl --kubeconfig /home/vagrant/.kube/config label node k8s-master node-role.kubernetes.io/master=master
            sudo kubectl --kubeconfig /home/vagrant/.kube/config label node k8s-node-1 node-role.kubernetes.io/worker=worker
            sudo kubectl --kubeconfig /home/vagrant/.kube/config label node k8s-node-2 node-role.kubernetes.io/worker=worker

            # Install helm
            curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
            sudo apt-get install apt-transport-https --yes
            echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
            sudo apt-get update
            sudo apt-get install helm

        SHELL

    end

end
