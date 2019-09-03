# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "centos/7"
  config.ssh.forward_agent = true
  config.ssh.insert_key = false
  

  config.vm.define "main-server" do |server|
    server.vm.hostname = 'main-server'
    server.vm.network :private_network, ip: '10.0.0.13'
    server.vm.network :forwarded_port, guest: 80, host: 45000
    server.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--memory", "9216"]
        vb.customize ["modifyvm", :id, "--cpus", "5"]
    end
  end


  config.vm.provision "ansible" do |ansible|
    ansible.verbose = "v"
    ansible.playbook = "provision/playbook.yml"
  end

  ############# MongoDB ######################
  N = 3
  (1..N).each do |machine_id|
    config.vm.define "mongo#{machine_id}" do |machine|
      machine.vm.hostname = "mongo#{machine_id}"
      machine.vm.network "private_network", ip: "10.0.0.#{13+machine_id}"
      machine.vm.provider :virtualbox do |vb|
          vb.customize ["modifyvm", :id, "--memory", "1024"]
          vb.customize ["modifyvm", :id, "--cpus", "1"]

      # Only execute once the Ansible provisioner,
      # when all the machines are up and ready.
      if machine_id == N
        machine.vm.provision :ansible do |ansible|
          # Disable default limit to connect to all the machines
          ansible.limit = "all"
          ansible.verbose = "v"
          ansible.playbook = "provision/playbook_mongo.yml"
          ansible.inventory_path = "provision/hosts"
          end
        end
      end
    end
  end
end
