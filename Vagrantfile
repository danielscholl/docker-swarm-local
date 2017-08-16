# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # Set the Box OS
  config.vm.box = "ubuntu/xenial64"

  # Customize the machine and add a SATA drive controller for REX-ray.
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 512
    vb.cpus = 1
    vb.customize [ "modifyvm", :id, "--macaddress1", "auto" ]

    #  Note: Doing a vagrant up after first time creation will fail as it tries to add controller again.
    #  comment out this line to be able to do a vagrant halt and up again.
    vb.customize ["storagectl", :id, "--add", "sata", "--controller", "IntelAhci", "--name", "SATA", "--portcount", 30, "--hostiocache", "on"]
  end

  # Ansible configure the nodes.
  config.vm.provision "ansible" do |ansible|

    # Categorize nodes into the proper group to properly configure the swarm.
    ansible.groups = {
      "manager" => [
        "node1",
        "node2"
      ],
      "worker" => [
        "node3"
      ]
    }
    ansible.playbook = "playbooks/main.yml"
    ansible.limit = 'all'

    # By default the interface to configure the swarm is the private network.
    ansible.extra_vars = {
      "swarm_iface" => "enp0s8",
    }
  end

  # Create Node 1 for a Swarm Manager (Leader)
  config.vm.define "node1" do |config|
    config.vm.hostname = "node1"
    config.vm.network "private_network", ip: "10.0.7.11"
    config.vm.synced_folder "./shared/", "/home/ubuntu/shared"
  end

  # Create Node 2 as a Swarm Manager (Reachable)
  config.vm.define "node2" do |config|
    config.vm.hostname = "node2"
    config.vm.network "private_network", ip: "10.0.7.12"
  end

  # Create Node 3 as a Swarm Worker
  config.vm.define "node3" do |config|
    config.vm.hostname = "node3"
    config.vm.network "private_network", ip: "10.0.7.13"
  end
end
