# This Vagrantfile installs multiple test Centos 7 systems

# Install the following plugins:
# vagrant plugin install vagrant-hostmanager
# vagrant plugin install vagrant-proxyconf (set to your laptop proxy IP or external proxy)

# This defines how many testservers Vagrant has to build
TESTSERVER = 1


Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  if Vagrant.has_plugin?("vagrant-proxyconf")
    config.proxy.http     = "http://10.72.238.152:3128"
    config.proxy.https    = "http://10.72.238.152:3128"
    config.proxy.no_proxy = "localhost,127.0.0.1,.vagrant.test"
  end
  if Vagrant.has_plugin?("vagrant-hostmanager")
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.manage_guest = true
  end
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
      if i == TESTSERVER
        node.vm.provision :ansible do |ansible|
          ansible.playbook = "./ansible/site.yml"
          ansible.sudo = true
          ansible.verbose = "v"
          ansible.host_key_checking = false
          ansible.limit = "testserver_group"
          ansible.groups = {
            "testserver_group" => ["testserver[1:#{TESTSERVER}]"]
          }
        end
      end

    end
  end


end
