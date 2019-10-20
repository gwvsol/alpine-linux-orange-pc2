# -*- mode: ruby -*-
# vi: set ft=ruby :
PUBLIC_KEY_PATH = "#{Dir.home}/.ssh/id_rsa_vagrant.pub"
AARCH_GCC = "aarch64-linux-gnu.sh"
MAKE_UBOOT = "make-uboot"
SYNC_CONF = "sync.conf"
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64" # Какую используем сборку Linux
  config.vm.box_check_update = false  # Отключаем проверку обновления сборки
  config.vm.hostname = "VM02-Alpine-for-OrangePC2"
  config.disksize.size = '20GB'
  config.vm.network "public_network", ip: "192.168.10.33"
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.name = "orangepc"
    vb.memory = "2048"
    vb.cpus = "2"
  end
  config.vm.provision :shell, :inline => "export DEBIAN_FRONTEND=noninteractive && apt-get update && apt-get upgrade -y && apt-get -qq -y install git atop gawk wget unzip tzdata sed xz-utils bison flex python2.7 python-dev swig build-essential lzop u-boot-tools libncurses-dev"
  config.vm.provision :shell, :inline => "export GCCURL='https://releases.linaro.org/components/toolchain/binaries/latest-7/aarch64-linux-gnu' && export GCC='gcc-linaro-7.4.1-2019.02-x86_64_aarch64-linux-gnu' && export ARCH='tar.xz' && cd /tmp && wget $GCCURL/$GCC.$ARCH && tar -xvJf $GCC.$ARCH && mv $GCC /usr/lib/gcc/aarch64-linux-gnu && rm $GCC.$ARCH"
  config.vm.provision :file, source: MAKE_UBOOT, destination: '/tmp/make-uboot'
  config.vm.provision :file, source: SYNC_CONF, destination: '/tmp/sync.conf'
  config.vm.provision :file, source: AARCH_GCC, destination: '/tmp/aarch64-linux-gnu.sh'
  config.vm.provision :shell, :inline => "chmod +x /tmp/make-uboot && mv /tmp/aarch64-linux-gnu.sh /etc/profile.d/ && mv /tmp/make-uboot /home/vagrant/ && ln -s /home/vagrant/make-uboot /usr/local/bin/ && mv /tmp/sync.conf /home/vagrant/"
  if Pathname.new(PUBLIC_KEY_PATH).exist?
    config.vm.provision :file, source: PUBLIC_KEY_PATH, destination: '/tmp/id_rsa.pub'
    config.vm.provision :shell, :inline => "cat /tmp/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
  end
end

