BOX_IMAGE = "CentosBox/Centos7-v7.3-Minimal"
SETUP_MASTER = true
SETUP_NODES = true
NODE_COUNT = 2
MASTER_IP = "192.168.26.10"
NODE_IP_NW = "192.168.26."
POD_NW_CIDR = "10.244.0.0/16"

# This script will prepare the box for the future kubeadm installation
$script = <<-'SCRIPT'
# kubelet requires swap off
sudo swapoff -a
# keep swap off after reboot
sudo sed -i '/swap/d' /etc/fstab
sudo yum -y upgrade
sudo yum -y install git vim bash-completion
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = BOX_IMAGE
  config.vm.box_check_update = false

  config.vm.provider "virtualbox" do |l|
    l.cpus = 1
    l.memory = "1024"
  end

  if SETUP_MASTER
    config.vm.define "master" do |subconfig|
      subconfig.vm.hostname = "master"
      subconfig.vm.network :private_network, ip: MASTER_IP
      subconfig.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--usb", "on"]
        vb.customize ["modifyvm", :id, "--usbehci", "off"]
        vb.customize ["modifyvm", :id, "--cpus", "2"]
        vb.customize ["modifyvm", :id, "--memory", "2048"]
      end
      subconfig.vm.provision :shell, inline: $script
    end
  end
  
  if SETUP_NODES
    (1..NODE_COUNT).each do |i|
      config.vm.define "node#{i}" do |subconfig|
        subconfig.vm.hostname = "node#{i}"
        subconfig.vm.network :private_network, ip: NODE_IP_NW + "#{i + 10}"
        subconfig.vm.provider :virtualbox do |vb|
          vb.customize ["modifyvm", :id, "--usb", "on"]
          vb.customize ["modifyvm", :id, "--usbehci", "off"]
        end
        subconfig.vm.provision :shell, inline: $script
      end
    end
  end
end
