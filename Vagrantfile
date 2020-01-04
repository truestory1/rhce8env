# VAGRANT_DISABLE_VBOXSYMLINKCREATE = "1"

servers=[
  {
    :hostname => "node1.example.com",
    :ip => "192.168.55.201",
    :ram => 1024,
    :cpu => 1,
    :disk => "./disk-0-1.vdi",
    :disk_size => 1024 * 2,
    :playbook => "playbooks/base-provisioning.yml"
  },
  {
    :hostname => "node2.example.com",
    :ip => "192.168.55.202",
    :ram => 1024,
    :cpu => 1,
    :disk => "./disk-0-2.vdi",
    :disk_size => 1024 * 2,
    :playbook => "playbooks/base-provisioning.yml"
  },
  {
    :hostname => "node3.example.com",
    :ip => "192.168.55.203",
    :ram => 1024,
    :cpu => 1,
    :disk => "./disk-0-3.vdi",
    :disk_size => 1024 * 3,
    :playbook => "playbooks/base-provisioning.yml"
  },
  {
    :hostname => "control.example.com",
    :ip => "192.168.55.200",
    :ram => 2048,
    :cpu => 2,
    :playbook => "playbooks/base-provisioning.yml"
  }
]

Vagrant.configure(2) do |config|
  # Use same SSH key for each machine
  config.ssh.insert_key = false
  config.vm.box_check_update = false

  servers.each do |machine|
    config.vm.define machine[:hostname] do |node|
      node.vm.box = "centos/8"
      node.vm.hostname =  machine[:hostname]
      node.vm.network "private_network", ip: machine[:ip]
      node.vm.synced_folder ".", "/vagrant", type: "virtualbox"
      node.vm.provider "virtualbox" do |vb|
        vb.memory = machine[:ram]
        vb.cpus = machine[:cpu]
        
        if (!machine[:disk].nil?)
          if not File.exist?(machine[:disk])
            vb.customize ['createhd', '--filename', machine[:disk], '--variant', 'Fixed', '--size', machine[:disk_size]]
            vb.customize ['storagectl', :id, '--name', 'SATA Controller', '--add', 'sata', '--portcount', 2]
            vb.customize ['storageattach', :id,  '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', machine[:disk]]
          end #if
        end #if

      node.vm.provision "shell",
        inline: "umount /vagrant ; mount -t vboxsf -o uid=`id -u vagrant`,gid=`id -g vagrant`,dmode=755,fmode=644 vagrant /vagrant",
        run: "always"
      
      node.vm.provision :ansible_local do |ansible|
        ansible.playbook = machine[:playbook]
      end #vm.provision ansible
      end #vb
    end #node
  end #machine

end #config


# config.vm.define "control" do |control|
#   control.vm.box = "centos/8"
#   control.vm.hostname = "control.example.com"
#   control.vm.network "private_network", ip: "192.168.55.200"
#   control.vm.provider :virtualbox do |control|
#     control.customize ['modifyvm', :id,'--memory', '2048']
#     end
#   control.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: ".git/"
# #   control.vm.provision :ansible_local do |ansible|
# #   ansible.playbook = "/vagrant/playbooks/master.yml"
# #   ansible.inventory_path = "/vagrant/inventory"
# #   ansible.config_file = "/vagrant/ansible.cfg"
# #   ansible.limit = "all"
# #  end
# end
# end
