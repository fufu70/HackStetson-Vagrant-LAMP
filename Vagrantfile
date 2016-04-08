
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