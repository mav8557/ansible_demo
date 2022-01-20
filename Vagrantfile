# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "geerlingguy/centos7"

  config.vm.synced_folder ".", "/vagrant", disabled: true


  config.ssh.insert_key = false
  config.ssh.private_key_path = File.expand_path('~/.vagrant.d/insecure_private_key')

  config.vm.provider :virtualbox do |v|
    v.memory = 256
    v.linked_clone = true
  end

  # app server 1
  config.vm.define "app1" do |app|
    app.vm.hostname = "demo-app1.test"
    app.vm.network :private_network, ip: "192.168.56.10"
  end
  
  # app server 2
  config.vm.define "app2" do |app|
    app.vm.hostname = "demo-app2.test"
    app.vm.network :private_network, ip: "192.168.56.11"
  end


  # database
  config.vm.define "db" do |db|
    db.vm.hostname = "demo-db.test"
    db.vm.network :private_network, ip: "192.168.56.12"
  end

end
