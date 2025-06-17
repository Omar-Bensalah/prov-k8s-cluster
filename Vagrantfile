Vagrant.configure("2") do |config|
  config.vm.define "control-node" do |node|
    config.vm.box = "ubuntu/jammy64"
    config.vm.hostname = "control-node"
    config.vm.network "private_network", ip: "192.168.56.2"
  
    config.vm.provider "virtualbox" do |vb|
     vb.name = "control-node"
     vb.memory = "2048"
     vb.cpus = 2
    end
  end

  config.vm.provision "shell", inline: <<-SHELL
    # Update apt and install prerequisites
    sudo apt-get update
    sudo apt-get install -y curl gnupg software-properties-common

    # Install kubectl
    sudo mkdir -p /etc/apt/keyrings
    sudo curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
    echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
    sudo apt-get update && sudo apt-get install -y kubectl

    # Install Terraform for Ubuntu Jammy
    sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
    curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
    sudo apt-get update && sudo apt-get install -y terraform

    # Install Ansible
    sudo apt-add-repository --yes --update ppa:ansible/ansible
    sudo apt-get update && sudo apt-get install -y ansible

    # Install python3 and pip
    sudo apt-get install -y python3 python3-pip

    # Install git
    sudo apt-get install -y git

    # generating ssh key 
    ssh-keygen -t rsa -b 4096 -N "" -f ~/.ssh/id_rsa
    sudo systemctl restart sshd
  SHELL
end
