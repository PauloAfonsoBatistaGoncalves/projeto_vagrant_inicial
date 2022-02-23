Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.network "forwarded_port", guest: 80, host: 8089
  #config.vm.network "private_network", ip: "192.168.56.2"
  #config.vm.network "private_network", type: "dhcp"
  config.vm.network "public_network", bridge: "wlp5s0", ip: "192.168.101.15"
end
