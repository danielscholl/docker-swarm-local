# -*- mode: ruby -*-
# vi: set ft=ruby :

nummanagers = 2
numworkers = 1


Vagrant.configure("2") do |config|
  # Set the current $PWD as the place where you will
  # store the VMDKs that are attached to the VM.
  dir = "#{ENV['PWD']}/Volumes"

  memory = 512
  cpu = 1

  manager_nodes = []
  manager_instances = []
  (1..nummanagers).each do |n| 
    manager_instances.push({:name => "manager#{n}", :ip => "10.0.7.#{n+10}"})
    manager_nodes.push("manager#{n}")
  end

  worker_nodes = []
  worker_instances = []
  (1..numworkers).each do |n| 
    worker_instances.push({:name => "worker#{n}", :ip => "10.0.7.#{n+20}"})
    worker_nodes.push("worker#{n}")
  end

  config.vm.box = "ubuntu/xenial64"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = memory
    vb.cpus = cpu
    vb.customize [ "modifyvm", :id, "--macaddress1", "auto" ]
    vb.customize ["storagectl", :id, "--add", "sata", "--controller", "IntelAhci", "--name", "SATA", "--portcount", 30, "--hostiocache", "on"]
  end

  config.vm.provision "ansible" do |ansible|
    ansible.groups = {
      "manager" => manager_nodes,
      "worker" => worker_nodes
    }
    ansible.playbook = "playbooks/main.yml"
    ansible.limit = 'all'
    ansible.extra_vars = { 
      "swarm_iface" => "enp0s8", 
    }
  end


  manager_instances.each do |instance| 
    config.vm.define instance[:name] do |i|
      i.vm.hostname = instance[:name]
      i.vm.network "private_network", ip: "#{instance[:ip]}"
      # i.vm.provision "ansible" do |ansible|
      #   ansible.groups = {
      #     "manager" => manager_nodes,
      #     "worker" => worker_nodes
      #   }
      #   ansible.playbook = "playbooks/main.yml"
      #   ansible.limit = 'all'
      #   ansible.extra_vars = { 
      #     "swarm_iface" => "enp0s8", 
      #   }
      # end
    end
  end

  worker_instances.each do |instance| 
    config.vm.define instance[:name] do |i|
      i.vm.hostname = instance[:name]
      i.vm.network "private_network", ip: "#{instance[:ip]}"
      # i.vm.provision "ansible" do |ansible|
      #   ansible.groups = {
      #     "manager" => manager_nodes,
      #     "worker" => worker_nodes
      #   }
      #   ansible.playbook = "playbooks/main.yml"
      #   ansible.limit = 'all'
      #   ansible.extra_vars = { 
      #     "swarm_iface" => "enp0s8", 
      #   }
      # end
    end
  end

end