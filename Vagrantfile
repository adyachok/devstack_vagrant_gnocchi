# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.hostname = "stack"
  config.vm.network "private_network", ip: "10.20.0.10"
  config.vm.network "public_network"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"
    vb.cpus = "2"
    vb.name = "stack"
  end

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y vim python-pip git
    pip install -U pip
    apt-get install -y python-dev libffi-dev gcc libssl-dev python-selinux

    # Devstack logic
    useradd -s /bin/bash -d /opt/stack -m stack
    echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack       
  SHELL

  config.vm.provision "shell", inline: <<-SHELL
    sudo su -l stack -c "git clone https://git.openstack.org/openstack-dev/devstack"
    sudo su -l stack -c "cd devstack"

    # Create the local.conf file
    sudo su -l stack -c "cp ~/devstack/samples/local.conf ~/devstack/"
    sudo su -l stack -c "echo 'HOST_IP=10.20.0.10' >> ~/devstack/local.conf"
    
    # Add Gnocchi service
    sudo su -l stack -c "echo 'enable_plugin gnocchi https://github.com/openstack/gnocchi master' >> ~/devstack/local.conf"
    sudo su -l stack -c "echo 'enable_service gnocchi-api,gnocchi-metricd\' >> ~/devstack/local.conf"

    #Run Devstack installation
    sudo su -l stack -c "~/devstack/stack.sh"
  SHELL

end