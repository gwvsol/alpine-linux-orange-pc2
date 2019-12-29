# -*- mode: ruby -*-
# vi: set ft=ruby :
PUBLIC_KEY_PATH = "#{Dir.home}/.ssh/id_rsa_vagrant.pub"
CONF_KERNELL = "config-4.19.91"
MAKE_DISTR = "make-distr"
CONF_ENV = "aarch64-linux-musl.sh"
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.box_check_update = false
  config.vm.hostname = "VM06-Alpine-for-OrangePC2"
  config.disksize.size = '20GB'
  config.vm.network "public_network", ip: "192.168.10.36"
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.name = "orangepc2"
    vb.memory = "2048"
    vb.cpus = "2"
  end
  config.vm.provision :shell, :inline => "export DEBIAN_FRONTEND=noninteractive && apt-get update && apt-get upgrade -y && apt-get -qq -y install git atop gawk wget unzip tzdata sed xz-utils bison flex python2.7 python-dev python-setuptools swig build-essential lzop u-boot-tools libncurses-dev libssl-dev python3-setuptools python3-dev"
  config.vm.provision :file, source: CONF_KERNELL , destination: '/tmp/config-4.19.91'
  config.vm.provision :file, source: MAKE_DISTR, destination: '/tmp/make-distr'
  config.vm.provision :file, source: CONF_ENV, destination: '/tmp/aarch64-linux-musl.sh'
  config.vm.provision :shell, :inline => "mkdir -p /home/vagrant/conf && mv /tmp/config-4.19.91 /home/vagrant/conf/ && chmod +x /tmp/make-distr && mv /tmp/make-distr /home/vagrant/conf/ && chown -R vagrant:vagrant /home/vagrant/conf && mv /tmp/aarch64-linux-musl.sh /etc/profile.d/ && ln -s /home/vagrant/conf/make-distr /usr/local/bin/"
  if Pathname.new(PUBLIC_KEY_PATH).exist?
    config.vm.provision :file, source: PUBLIC_KEY_PATH, destination: '/tmp/id_rsa.pub'
    config.vm.provision :shell, :inline => "cat /tmp/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
  end
end

