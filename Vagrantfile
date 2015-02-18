# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "centos65_x86_64"
  ##config.vm.box = "https://github.com/2creatives/vagrant-centos/releases/download/v6.5.1/centos65-x86_64-20131205.box"
  config.vm.boot_timeout = 600
  config.vm.network :public_network, ip: "192.168.10.33", bridge: 'en1: Wi-Fi (AirPort)'
  config.vm.provider :virtualbox do |vb|
     vb.customize ["modifyvm", :id, 
                   "--memory", "4096",
                   "--nicpromisc1", "allow-all",
                   "--nicpromisc2", "allow-all"]
  end
  
  ## omnibus
  config.omnibus.chef_version = :latest

  ## network configuration
  #config.vm.provision :shell, :path => "bootstrap.sh"

  ## provisioning by chef
  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = "./vendor/cookbooks/"
    chef.run_list = [
      "rvm::system",
      "git",
      "nodejs",
      "openssl",
      "yum"
    ]
  end
end
