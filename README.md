# vagrant-testservers
Quickly deploy a few Centos Testservers with Vagrant, very handy for testing stuff or practicing with RHEL7.
## Prerequisites
Install VirtualBox
Install Vagrant
Install Vargant plugin vagrant-hostmanager
## Howto
Clone this repo and run Vagrant:
```sh
git clone https://github.com/scorputty/vagrant-testservers.git
cd vagrant-testservers
vagrant up
```
This will install two testserver nodes, testserver1 and testserver2. You can login to them with vagrant ssh or ssh vagrant@testserverX (pass = vagrant).
If you want more or less nodes to play with you need to edit this field in the Vagrantfile:
```ruby
TESTSERVER = 2
```
## Note
Your sudo pass might be asked to enable the hostmanager plugin to update your hosts file.
## Note 2
This has only been tested on Mac OSX
