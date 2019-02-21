Vagrant.configure("2") do |config|
	config.vm.box = "bento/centos-7.5"
		config.vm.provider "virtualbox" do |vb|
		vb.memory = 1024
	end
	
	config.vm.define "vm1" do |vm1|
		vm1.vm.network :private_network, ip: "192.168.56.51"
		vm1.vm.hostname = "vm1"
		vm1.vm.provision "shell" , 
			inline: <<-SHELL
				yum install -y git
				cd /home
				if [ ! -d Module2 ]; then git clone -b task2 "https://github.com/3Hak/Module2.git"; fi
				more /home/Module2/test
			SHELL
	end
	
	config.vm.define "vm2" do |vm2|
		vm2.vm.network :private_network, ip: "192.168.56.52"
		vm2.vm.hostname = "vm2"
	end

	config.vm.provision "shell" ,
		inline: <<-SHELL
			grep -q "192.168.56.51 vm1" /etc/hosts || echo -e "192.168.56.51 vm1\n192.168.56.52 vm2" >> /etc/hosts
		SHELL
end