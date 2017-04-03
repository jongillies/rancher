# -*- mode: ruby -*-
# vi: set ft=ruby :

nodes_config = YAML.load_file('nodes.yml')

Vagrant.configure(2) do |config|

  nodes_config.each do |node|

    config.ssh.username = 'ubuntu'

    config.vbguest.auto_update = true
    config.vbguest.iso_path = 'http://download.virtualbox.org/virtualbox/%{version}/VBoxGuestAdditions_%{version}.iso'

    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.ignore_private_ip = false
    config.hostmanager.include_offline = true

    config.vm.define node['name'] do |vm_config|

      vm_config.vm.box = node['box']

      vm_config.vm.hostname = node['name']
      vm_config.vm.network :private_network, ip: node['ip']

      vm_config.vm.boot_timeout = 900 # 15 minutes

      vm_config.vm.provider :virtualbox do |vb|
        vb.memory = node['ram']
        vb.cpus = node['cpus']
      end

      # Make sure python is installed for Ansible the provisioner
      config.vm.provision 'shell', inline: 'apt-get install -y python-minimal'

      config.vm.provision 'ansible' do |ansible|
        ansible.playbook = 'playbook.yml'
        ansible.sudo = true
        #ansible.verbose = 'vvv'
        ansible.extra_vars = node['extra_vars']
      end

    end

  end

end
