# -*- mode: ruby -*-
# vi: set ft=ruby :

require "yaml"
current_dir    = File.dirname(File.expand_path(__FILE__))
configs        = YAML.load_file("#{current_dir}/config.yaml")
vagrant_config = configs["configs"][configs["configs"]["use"]]

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don"t change it unless you know what
# you"re doing.
Vagrant.configure("2") do |config|
  VAGRANT_COMMAND = ARGV[0]
  if VAGRANT_COMMAND == "ssh"
    config.ssh.username = vagrant_config["username"]
    config.ssh.password = vagrant_config["password"]
    config.ssh.insert_key = true
  end

  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "bento/ubuntu-17.04"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Configure proxy settings
  # NOTE: This requires the vagrant-proxyconf plugin to be installed
  if configs["proxy"]
    config.proxy.http = configs["proxy"]
    config.proxy.https = configs["proxy"]
    if configs["no_proxy"]
      config.proxy.no_proxy = configs["no_proxy"]
    end
  end

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  config.vm.provider "virtualbox" do |vb|
    vb.gui = vagrant_config["gui"]
    vb.name = vagrant_config["hostname"]
    vb.memory = "1024"
    vb.cpus = "2"
    vb.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
    vb.customize ["modifyvm", :id, "--cpuhotplug", "on"]
    vb.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
    vb.customize ["modifyvm", :id, "--vram", "128"]
    vb.customize ["modifyvm", :id, "--accelerate3d", "on"]
    vb.customize ["modifyvm", :id, "--accelerate2dvideo", "on"]
    vb.customize ["modifyvm", :id, "--hwvirtex", "on"]
  end
  # View the documentation for the provider you are using for more
  # information on available options.

  config.vm.hostname = vagrant_config["hostname"]

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  config.vm.provision "docker"

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "system", type: "shell", path: "bootstrap.sh", env: {
    "LOCALE" => vagrant_config["locale"],
    "KEYMAP" => vagrant_config["keymap"],
    "TIMEZONE" => vagrant_config["timezone"],
    "GUI" => vagrant_config["gui"]
  }

  # User specific setup
  config.vm.provision "user", type: "shell", path: "bootstrap-user.sh", env: {
    "USERNAME" => vagrant_config["username"],
    "USERMAIL" => vagrant_config["usermail"],
    "USERTOOLS" => vagrant_config["usertools"],
    "PASSWORD" => vagrant_config["password"],
    "GUI" => vagrant_config["gui"]
  }

  # This requires the vagrant-reload plugin to be installed
  config.vm.provision :reload
end
