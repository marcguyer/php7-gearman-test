# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/xenial64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.100"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get upgrade -y

    echo "Install gearman-job-server"
    apt-get install -y gearman-job-server gearman-tools

    echo "Configure gearman-job-server to listen on private_network ip"
    mkdir /etc/systemd/system/gearman-job-server.service.d
    echo '[Service]
    ExecStart=
    ExecStart=/usr/sbin/gearmand --pid-file=/run/gearman/server.pid --log-file=/var/log/gearman-job-server/gearman.log --verbose=NOTICE --round-robin' >/etc/systemd/system/gearman-job-server.service.d/override.conf
    systemctl daemon-reload
    systemctl restart gearman-job-server.service
    systemctl status gearman-job-server.service

    echo "Install php with gearman client"
    apt-get install -y php-cli php-dev libgearman-dev unzip
    wget https://github.com/wcgallego/pecl-gearman/archive/master.zip
    unzip master.zip
    rm master.zip
    cd pecl-gearman-master
    phpize
    ./configure
    make
    make install
    echo "extension=gearman.so\n" > /etc/php/7.0/mods-available/gearman.ini
    phpenmod gearman

    echo "Set the examples to point to the private_network ip"
    sed -i -- "s/>addServer();/>addServer('192.168.33.100','4730');/g" examples/*.php
  SHELL
end
