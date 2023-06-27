# -*- mode: ruby -*-
# vi: set ft=ruby :

MACHINES = {
  :master => '192.168.56.150',
  :slave => '192.168.56.151'
}

Vagrant.configure(2) do |config|
  config.vm.provider :virtualbox
  config.vm.box_check_update = false
  config.vm.box = 'almalinux/9'
  config.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "playbook.yml"
  end
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024
  end
  MACHINES.each do |boxname, ip_address|
    config.vm.define boxname do |box|
      box.vm.host_name = boxname.to_s
      box.vm.network "private_network", ip: ip_address
    end
  end
end
