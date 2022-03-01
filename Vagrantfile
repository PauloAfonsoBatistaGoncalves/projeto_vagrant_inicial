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
      phpweb.vm.network "forwarded_port", guest: 80, host: 8089
      phpweb.vm.network "public_network", bridge: "wlp5s0", ip: "192.168.101.16"
      phpweb.vm.provision "shell",
          inline: "sudo apt-get update"
    phpweb.vm.provision "shell",
          inline: "sudo apt-get install -y puppet"
  end
end
