Vagrant.configure("2") do |config|
   config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024
    vb.cpus = 2
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end
  config.vm.define "containermaster" do |subconfig|
    subconfig.vm.box = "CentosBox/Centos7-v7.3-Minimal"
    subconfig.vm.hostname = "containermaster"
    subconfig.vm.network :private_network, ip: "10.0.0.17"
    subconfig.vm.provision "shell", inline: <<-SHELLMASTER
    sudo echo "containermaster" >> /etc/motd
    SHELLMASTER
  end
  config.vm.define "containernode1" do |subconfig|
    subconfig.vm.box = "CentosBox/Centos7-v7.3-Minimal"
    subconfig.vm.hostname = "containernode1"
    subconfig.vm.network :private_network, ip: "10.0.0.18"
    subconfig.vm.provision "shell", path: "SHELLNODE" 
  end
  config.vm.define "containernode2" do |subconfig|
    subconfig.vm.box = "CentosBox/Centos7-v7.3-Minimal"
    subconfig.vm.hostname = "containernode2"
    subconfig.vm.network :private_network, ip: "10.0.0.19"
    config.vm.provision "shell", path: "SHELLNODE"
  end
  # Install avahi on all machines  
  config.vm.provision "shell", inline: <<-SHELL   
   sudo swapoff -a
   sudo yum -y install yum-utils device-mapper-persistent-data lvm2
   sudo yum -y install vim curl wget
   sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
   sudo yum-config-manager  --enable docker-ce-stable-x86_64
   sudo yum-config-manager --enable docker-ce-test
   sudo yum-config-manager --enable docker-ce-nightly/
   sudo yum -y update
   sudo yum -y install docker-ce docker-ce-cli containerd.io
   sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
   sudo echo "docker-compose migrate-to-labels" >> /etc/motd
   sudo systemctl enable --now docker
   sudo swapoff -a
   sudo echo "10.0.0.17       containermaster" >> /etc/hosts
   sudo echo "10.0.0.18       containernode1"  >> /etc/hosts
   sudo echo "10.0.0.19       containernode2"  >> /etc/hosts
  SHELL
  config.vm.provider :virtualbox do |vb|
  vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
end
end
