Vagrant.configure("2") do |config|
	config.vm.box = "bento/centos-7.5"
		config.vm.provider "virtualbox" do |vb|
		vb.memory = "1024"
	end
	
	config.vm.define "t1" do |t1|
		t1.vm.network :private_network, ip: "192.168.56.11"
		t1.vm.hostname = "tom1"
		t1.vm.provision "shell" , 
			inline: <<-SHELL
				yum -y install java-1.8.0-openjdk tomcat tomcat-webapps tomcat-admin-webapps
				systemctl enable tomcat
				systemctl start tomcat
				mkdir -p /usr/share/tomcat/webapps/content
				touch /usr/share/tomcat/webapps/content/index.html
				echo "it's tommy1" > /usr/share/tomcat/webapps/content/index.html
			SHELL
	end
	
	config.vm.define "t2" do |t2|
		t2.vm.network :private_network, ip: "192.168.56.12"
		t2.vm.hostname = "tom2"
		t2.vm.provision "shell" , 
			inline: <<-SHELL
				yum -y install java-1.8.0-openjdk tomcat tomcat-webapps tomcat-admin-webapps
				systemctl enable tomcat
				systemctl start tomcat
				mkdir -p /usr/share/tomcat/webapps/content
				touch /usr/share/tomcat/webapps/content/index.html
				echo "it's tommy2" > /usr/share/tomcat/webapps/content/index.html
			SHELL
	end
	
	config.vm.define "main" do |main|
		main.vm.network :private_network, ip: "192.168.56.10"
		main.vm.network :forwarded_port, guest: 80, host: 16577
		main.vm.hostname = "main"
		main.vm.provision "shell" , 
			inline: <<-SHELL
				yum -y install httpd
				systemctl enable httpd
				systemctl start httpd
				cp -n /vagrant/mod_jk.so /etc/httpd/modules/
				echo "worker.list=lb" > /etc/httpd/conf/workers.properties
				echo "worker.lb.type=lb" >> /etc/httpd/conf/workers.properties
				echo "worker.lb.balance_workers=wk1, wk2" >> /etc/httpd/conf/workers.properties
				echo "worker.wk1.host=tom1" >> /etc/httpd/conf/workers.properties
				echo "worker.wk1.port=8009" >> /etc/httpd/conf/workers.properties
				echo "worker.wk1.type=ajp13" >> /etc/httpd/conf/workers.properties
				echo "worker.wk2.host=tom2" >> /etc/httpd/conf/workers.properties
				echo "worker.wk2.port=8009" >> /etc/httpd/conf/workers.properties
				echo "worker.wk2.type=ajp13" >> /etc/httpd/conf/workers.properties
				
				grep -q "LoadModule jk_module modules/mod_jk.so" /etc/httpd/conf/httpd.conf || echo -e "LoadModule jk_module modules/mod_jk.so" >> /etc/httpd/conf/httpd.conf
				grep -q "JkWorkersFile conf/workers.properties" /etc/httpd/conf/httpd.conf || echo -e "JkWorkersFile conf/workers.properties" >> /etc/httpd/conf/httpd.conf
				grep -q "JkShmFile /tmp/shm" /etc/httpd/conf/httpd.conf || echo -e "JkShmFile /tmp/shm" >> /etc/httpd/conf/httpd.conf
				grep -q "JkLogFile logs/mod_jk.log" /etc/httpd/conf/httpd.conf || echo -e "JkLogFile logs/mod_jk.log" >> /etc/httpd/conf/httpd.conf
				grep -q "JkLogLevel info" /etc/httpd/conf/httpd.conf || echo -e "JkLogLevel info" >> /etc/httpd/conf/httpd.conf
				grep -q "JkMount /content* lb" /etc/httpd/conf/httpd.conf || echo -e "JkMount /content* lb" >> /etc/httpd/conf/httpd.conf
				service httpd restart
			SHELL
	end

	config.vm.provision "shell" , 
		inline: <<-SHELL
			yum install -y mc
			grep -q "192.168.56.10 main" /etc/hosts || echo -e "192.168.56.10 main\n192.168.56.11 tom1\n192.168.56.12 tom2" >> /etc/hosts
		SHELL
end
