# This Vagrantfile installs multiple test Centos 7 systems

# Install the following plugins:
# vagrant plugin install vagrant-hostmanager

# This defines how many testservers Vagrant has to build
TESTSERVER = 2


Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.vm.synced_folder ".", "/home/vagrant/sync", disabled: true
  config.vm.provider "virtualbox" do |v|
    v.linked_clone = true
  end


  (1..TESTSERVER).each do |i|
    config.vm.define "testserver#{i}" do |node|
      node.vm.box = "scorputty/centos"
      node.vm.hostname = "testserver#{i}"
      node.vm.network :private_network, ip: "192.168.0.%d" % (10 + i )
      node.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        unless File.exists?("2nd-disk-testserver#{i}.vdi")
          vb.customize ["createhd",  "--filename", "2nd-disk-testserver#{i}", "--size", "20480"] # 20Gb
        end
        vb.customize ["storageattach", :id, "--storagectl", "IDE Controller", "--port", "1", "--device", "1", "--type", "hdd", "--medium", "2nd-disk-testserver#{i}.vdi"]
      end
      # Use this block for Ansible provisioning
      # if i == TESTSERVER
      #   node.vm.provision :ansible do |ansible|
      #     ansible.playbook = "site.yml"
      #     ansible.sudo = true
      #     ansible.verbose = "v"
      #     ansible.host_key_checking = false
      #     ansible.limit = "testserver_group"
      #     ansible.groups = {
      #       "testserver_group" => ["gatewayserver[1:#{TESTSERVER}]"]
      #     }
      #   end
      # end

    end
  end


end
