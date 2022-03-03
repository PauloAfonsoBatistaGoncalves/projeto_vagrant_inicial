$script_mysql = <<-SCRIPT
 apt-get update && \
 apt-get install -y mysql-server-5.7 && \\
 mysql -e "create user 'phpuser'@'%' identified by 'pass';"
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  
  config.vm.define "mysqldb" do |mysql|
      mysql.vm.network "public_network", bridge: "wlp5s0", ip: "192.168.101.15"
      mysql.vm.provision "shell",
          inline: "cat /configs/public_key/vagrant-key.pub >> .ssh/authorized_keys"
      mysql.vm.provision "shell", inline: $script_mysql
      mysql.vm.provision "shell",
          inline: "cat /configs/mysqld.cnf >> /etc/mysql/mysql.conf.d/mysqld.cnf"
      mysql.vm.provision "shell",
          inline: "service mysql restart"
      mysql.vm.synced_folder "./configs", "/configs"
  end

  config.vm.define "phpweb" do |phpweb|
      phpweb.vm.network "forwarded_port", guest: 8888, host: 8888
      phpweb.vm.network "public_network", bridge: "wlp5s0", ip: "192.168.101.16"
      phpweb.vm.provision "shell",
          inline: "apt-get update && apt-get install -y puppet"
      
      phpweb.vm.provision "puppet" do |puppet|
          puppet.manifests_path = "./configs/manifests"
          puppet.manifest_file = "phpweb.pp"
      end
  end

  config.vm.define "mysqlserver" do |mysqlserver|
      mysqlserver.vm.network "public_network", bridge: "wlp5s0", ip: "192.168.101.17"
      mysqlserver.vm.synced_folder "./configs", "/configs"
      mysqlserver.vm.provision "shell",
          inline: "cat /configs/public_key/vagrant-key.pub >> .ssh/authorized_keys"
  end
   
  config.vm.define "ansible" do |ansible|
      ansible.vm.network "public_network", bridge: "wlp5s0", ip: "192.168.101.18"
      ansible.vm.synced_folder "./configs", "/configs"
      ansible.vm.provision "shell",
          inline: "cat /configs/public_key/vagrant-key.pub >> .ssh/authorized_keys"
      ansible.vm.provision "shell",
          inline: "cp /configs/private_key/vagrant-key /home/vagrant && \
                    chmod 600 /home/vagrant/vagrant-key && \
                    chown vagrant:vagrant /home/vagrant/vagrant-key"
      ansible.vm.provision "shell",
          inline: "apt-get update && \
                   apt install -y software-properties-common && \
                   add-apt-repository --yes --update ppa:ansible/ansible && \
                   apt install ansible -y"
     ansible.vm.provision "shell",
          inline: "ansible-playbook -i  /vagrant/configs/ansible/hosts \
                   /vagrant/configs/ansible/playbook.yaml"
  end
  
end