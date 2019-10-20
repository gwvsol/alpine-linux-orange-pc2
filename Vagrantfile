# -*- mode: ruby -*-
# vi: set ft=ruby :
PUBLIC_KEY_PATH = "#{Dir.home}/.ssh/id_rsa_vagrant.pub"
GCCURL="https://releases.linaro.org/components/toolchain/binaries/latest-7/aarch64-linux-gnu"
GCC="gcc-linaro-7.4.1-2019.02-x86_64_aarch64-linux-gnu"
ARCH=.tar.xz
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64" # Какую используем сборку Linux
  config.vm.box_check_update = false  # Отключаем проверку обновления сборки
  config.vm.hostname = "VM02-Alpine-for-OrangePC2"
  config.vm.network "public_network", ip: "192.168.10.33"
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.name = "orangepc"
    vb.memory = "2048"
    vb.cpus = "2"
  end
  config.vm.provision :shell, :inline => "export DEBIAN_FRONTEND=noninteractive && apt-get update && apt-get upgrade -y && apt-get -qq -y install git atop gawk wget unzip tzdata sed xz-utils bison flex python2.7 python-dev swig build-essential lzop u-boot-tools libncurses-dev"
  config.vm.provision :shell, :inline => "cd /tmp && wget https://releases.linaro.org/components/toolchain/binaries/latest-7/aarch64-linux-gnu/gcc-linaro-7.4.1-2019.02-x86_64_aarch64-linux-gnu.tar.xz && tar -xvJf gcc-linaro-7.4.1-2019.02-x86_64_aarch64-linux-gnu.tar.xz && mv gcc-linaro-7.4.1-2019.02-x86_64_aarch64-linux-gnu /usr/lib/gcc/gcc-linaro"
  if Pathname.new(PUBLIC_KEY_PATH).exist?
    config.vm.provision :file, source: PUBLIC_KEY_PATH, destination: '/tmp/id_rsa.pub'
    config.vm.provision :shell, :inline => "rm -f /home/vagrant/.ssh/authorized_keys && mkdir -p /home/vagrant/.ssh && sudo cp /tmp/id_rsa.pub /home/vagrant/.ssh/authorized_keys"
  end
end

