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
  config.vm.box = "bento/ubuntu-18.04"

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
  # config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.network "private_network", ip: "192.168.50.25"
  config.vm.hostname = "heroku4ancgis.dev.net"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Getting BrowserSync working with Vagrant
  #config.vm.network :forwarded_port, guest: 80, host: 8080, auto_correct: true
  #config.vm.network :forwarded_port, guest: 3001, host: 3001, auto_correct: true

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Disable the default root
  config.vm.synced_folder ".", "/vagrant", disabled: true

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
  config.vm.provider "virtualbox" do |v|
    v.memory = 4096
    v.cpus = 2
    v.name = "heroku4ancgis-server"
  end

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
  config.vm.provision "shell", privileged: true, inline: <<-SHELL

    # apt update and upgrade
    apt-get update
    DEBIAN_FRONTEND=noninteractive apt-get -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" upgrade

    # Node and npm (npm is distributed with Node.js)
    apt-get install -y curl
    curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
    apt-get install -y nodejs
    # To compile and install native addons from npm you may also need to install build tools
    apt-get install -y build-essential

    # MongoDB
    apt-get install -y mongodb

	  # Heroku
	  #snap install --classic heroku # snap server is very unstable in this moment.
	  curl https://cli-assets.heroku.com/install.sh | sh # It contains its own node.js
    heroku plugins:install heroku-repo # Install the Heroku Repo plugin (useful to reset the git remote repo).

    # Git
	  apt-get install -y git
    cd ~ && git clone https://github.com/sgalopin/ancgis.git
    cd ~/ancgis
    git remote add heroku https://git.heroku.com/ancgis.git # Can also be done via the heroku's "create" command
    git remote set-url --push origin no_push

  SHELL

  # The following provisions are only run when called explicitly
  if ARGV.include? '--provision-with'

    # Provision "create"
    config.vm.provision "create", type: "shell", privileged: false, inline: <<-SHELL
      heroku login # You must create an account on heroku before playing this command
      heroku create ancgis
      heroku addons:create mongolab:sandbox -a ancgis
      heroku ps:scale web=1 # Activate a single free dyno
    SHELL

    # Provision "test"
    config.vm.provision "test", type: "shell", privileged: false, inline: <<-SHELL
      cd ~/ancgis/application && npm install && npm run build
      # Get the MONGODB_URI env variable
      source ~/ancgis/application/.env
      # Set the MONGOLAB_URI env variable for heroku
      MONGOLAB_URI="$MONGODB_URI"
      heroku local web
    SHELL

    # Provision "populate-db"
    config.vm.provision "populate-db", type: "shell", privileged: false, inline: <<-SHELL
      /bin/bash ~/ancgis/shell/populate-db.sh
    SHELL

    # Provision "deploy"
    config.vm.provision "deploy", type: "shell", privileged: false, inline: <<-SHELL
      cd ~/ancgis
      git fetch origin
      git pull
      git subtree push --prefix application heroku master
      heroku open -a ancgis # Ouvre le navigateur à l'adresse https://ancgis.herokuapp.com
    SHELL

    # Provision "deploy"
    config.vm.provision "update-src", type: "shell", privileged: false, inline: <<-SHELL
      cd ~/ancgis
      git fetch origin
      git pull
    SHELL

  end

end
