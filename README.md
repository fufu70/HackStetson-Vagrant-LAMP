##WubbaLubbadubdub!!!

Wanna setup [Vagrant NodeJS instead?](https://github.com/fufu70/HackStetson-Vagrant-NodeJS)

###Setting up vagrant

So your trying to setup a basic LAMP environment for your team mates but its taking too much time, TIME TO SETUP VAGRANT!

First download [Vagrant](https://www.vagrantup.com/downloads.html) and [Virtual Box](https://www.virtualbox.org/wiki/Downloads)

Now that we have installed our dependencies lets start setting up vagrant

First lets type in the  in your console: 

> $ vagrant init 

Now lets setup the Vagrantfile to install a basic virtual box:

```
Vagrant.configure(2) do |config|

	config.vm.box = "precise32"

	config.vm.box_url = "http://files.vagrantup.com/precise32.box"
	
end
```


Once we setup the specific box to be used we can then start vagrant to install the box:

> $ vagrant up

While the box is installing we can setup a user and password for the box and a working directory both locally and on the server in the Vagrantfile:

```
Vagrant.configure(2) do |config|

	config.vm.box = "precise32"

	config.vm.box_url = "http://files.vagrantup.com/precise32.box"

	config.ssh.username = "vagrant"
	config.ssh.password = "vagrant"

	config.vm.synced_folder "src/", "/var/www/website"
	
end
```
---

### Building your Shell script

After the vagrant box is setup and a working directory we need to setup the LAMP environment on the server. We will be doing this through a basic shell script.

Firstly we need to create a shell script, I called mine "script.sh". My command is to firstly update the box:

```shell
#!/bin/bash
sudo apt-get -y update 

Then we want to install apache:

sudo apt-get -y install apache2
```

Once we have apache installed our goal is to setup Mysql. To do this we first need to define the root passwords we want to use (where "rootpass" is the root password):

```shell
sudo debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password password rootpass'
sudo debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password_again password rootpass'
```

And then install mysql automatically without user input:

```shell
sudo apt-get -y install mysql-server libapache2-mod-auth-mysql php5-mysql
```

After installing mysql we are on to php! To do this simply call:

```shell
sudo apt-get -y install php5 libapache2-mod-php5 php5-mcrypt
```

After we have everything installed we need to set the root directory of the apache server (in our case its "/var/www/website") ... and restart the server afterwards:

```shell
sudo sed -i 's/\/var\/www/\/var\/www\/website/g' /etc/apache2/sites-enabled/000-default
sudo service apache2 restart
```

Once we've added all of that to the "setup.sh" file we go into the Vagrantfile to define the startup script for our LAMP environment:

```
Vagrant.configure(2) do |config|

	config.vm.box = "precise32"

	config.vm.box_url = "http://files.vagrantup.com/precise32.box"

	# Mentioning the SSH Username/Password:
	config.ssh.username = "vagrant"
	config.ssh.password = "vagrant"

	config.vm.synced_folder "src/", "/var/www/website"

	# Begin Configuring
	config.vm.define "lamp" do|lamp|
		lamp.vm.hostname = "lamp" # Setting up hostname
		lamp.vm.network "private_network", ip: "192.168.205.10" # Setting up machine's IP Address
		lamp.vm.provision :shell, path: "setup.sh" # Provisioning with script.sh
	end

	# End Configuring
end
```
---

After all of this is finished we can then simply run

> $ vagrant reload --provision

to apply our changes to the vagrant server

Once your done with your vagrant box turn it off by running 

> $ vagrant halt

Or completly wipeout your box to start fresh by calling 

> $ vagrant destroy



