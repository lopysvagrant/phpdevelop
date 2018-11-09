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
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "lopysvagrant/debianjessie64"

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

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
  	# tmp dir
	mkdir /home/vagrant/.tmp
	cd /home/vagrant/.tmp
	chown -R vagrant:vagrant .


	# apt install
	echo "install php......................................................................................"
    apt-get update
  	apt-get install -y nginx php5-fpm php5-curl php5-gd php5-intl php-pear php5-imagick php5-imap \
			php5-mcrypt php5-common php5-mysql php5-pspell php5-recode php5-dev php5-sqlite \
			php5-tidy php5-xmlrpc php5-xsl php5-memcached git curl vim apt-transport-https ca-certificates  \
			gnupg2 software-properties-common
			
	# docker
	echo "install docker......................................................................................"
	curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | sudo apt-key add -
    apt-key fingerprint 0EBFCD88
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
    apt-get update
    apt-get install -y docker-ce
	

    # swoole
	echo "install swoole......................................................................................"
    wget "https://github.com/swoole/swoole-src/archive/v1.10.6.tar.gz"	
    tar -zxvf v1.10.6.tar.gz
    cd swoole-src-1.10.6
    phpize
    ./configure
    make && make install
    echo "extension=swoole.so" > /etc/php5/fpm/conf.d/swoole.ini
    echo "extension=swoole.so" > /etc/php5/cli/conf.d/swoole.ini
    echo "extension=swoole.so" > /etc/php5/mods-available/swoole.ini

    echo "check swoole info ......................................................................................."
    php --ri swoole 
    cd ..
    rm -rf v1.10.6.tar.gz swoole-src-1.10.6

    # 
    ## 配置一下 swap ，免得出错
	echo "option switchy area......................................................................................"
    /bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024
    /sbin/mkswap /var/swap.1
    /sbin/swapon /var/swap.1

    ##################
	# 切换 vagrant 用户操作
    ##################
	echo "su vagrant for some operation......................................................................................"
    su - vagrant << USER_VAGRANT

    # node npm bower hexo
	cd ~/.tmp

    ## instal nvm
	echo "install nvm......................................................................................"

	wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
    #~/.bash_profile, ~/.zshrc, ~/.profile, or ~/.bashrc
    echo "source /home/vagrant/.nvm/nvm.sh" >> ~/.bashrc
	source ~/.nvm/nvm.sh

    ## install node
	sleep 5s
    nvm ls-remote
#nvm install 8
	nvm install v8.12.0
    npm install -g hexo-cli http-server
	
	
	# install composer
	echo "install composer......................................................................................"
    curl -sS https://getcomposer.org/installer | php
    ./composer.phar global require 'composer/composer:dev-master'
    ./composer.phar global require 'phpunit/phpunit:5.7.*'
    echo "export PATH=/home/vagrant/.composer/vendor/bin:\\$PATH" >> ~/.bashrc
    source ~/.bashrc
    exit;
    USER_VAGRANT
	
    # 

    # fpm restart

	echo "restart php5-fpm......................................................................................"
    service php5-fpm restart
    
	echo "clean clean clean......................................................................................"
    apt-get clean
    apt-get autoclean
	rm -rf /home/vagrant/.tmp/*	
  SHELL
end
