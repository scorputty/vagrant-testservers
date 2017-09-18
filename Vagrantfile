# This Vagrantfile installs multiple test Centos 7 systems

# Install the following plugins:
# vagrant plugin install vagrant-hostmanager
# vagrant plugin install vagrant-proxyconf (set to your laptop proxy IP or external proxy)

# This defines how many testservers Vagrant has to build
TESTSERVER = 1
TESTDISKS = 4

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  if Vagrant.has_plugin?("vagrant-proxyconf")
    config.proxy.http     = "http://yourIPhere:3128"
    config.proxy.https    = "http://yourIPhere:3128"
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
        (1..TESTDISKS).each do |d|
          unless File.exists?("Disk-#{d}-testserver#{i}.vdi")
            vb.customize ["createhd",  "--filename", "Disk-#{d}-testserver#{i}", "--size", "20480"] # 20Gb
          end
        end
        vb.customize ['storagectl', :id, '--name', 'SATA Controller', '--add', 'sata', '--portcount', "#{TESTDISKS}"]
        (1..TESTDISKS).each do |d|
          vb.customize ["storageattach", :id, "--storagectl", "SATA Controller", "--port", "#{d}", "--device", 0, "--type", "hdd", "--medium", "Disk-#{d}-testserver#{i}.vdi"]
        end
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
