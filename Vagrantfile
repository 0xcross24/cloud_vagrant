Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2204"
  config.ssh.forward_agent    = true
  config.ssh.insert_key       = false
  config.ssh.private_key_path =  ["~/.vagrant.d/insecure_private_key","~/.ssh/vagrant"]
  config.vm.provision :shell, privileged: false do |s|
    ssh_pub_key = File.readlines("#{Dir.home}/.ssh/vagrant.pub").first.strip
    s.inline = <<-SHELL
     echo #{ssh_pub_key} >> /home/$USER/.ssh/authorized_keys
   SHELL
  end

  def puppet_agent(vm_config)
    vm_config.vm.provision "shell", inline: <<-SHELL
      sudo wget http://apt.puppetlabs.com/puppet7-release-jammy.deb
      sudo dpkg -i puppet7-release-jammy.deb
      sudo apt update -y
      sudo apt upgrade -y
      sudo apt install -y puppet-agent
      sudo echo "192.168.1.100 puppet" >> /etc/hosts
    SHELL
  end 

  config.vm.define "puppetmaster_01" do |puppetmaster_01|
    puppetmaster_01.vm.provider "virtualbox" do |v|
      v.memory = 3096
      v.cpus = 2
    end
    puppetmaster_01.vm.network "public_network", ip: "192.168.1.100", bridge: "enp4s0f2np2"
    puppetmaster_01.vm.hostname = "puppetmaster-01"
    puppetmaster_01.vm.provision "shell", inline: <<-SHELL
      sudo wget http://apt.puppetlabs.com/puppet7-release-jammy.deb
      sudo dpkg -i puppet7-release-jammy.deb
      sudo apt update -y 
      sudo apt upgrade -y
      sudo apt install -y puppetserver 
      sudo echo "192.168.1.100 puppet" >> /etc/hosts
      sudo systemctl enable puppetserver.service
      sudo systemctl start puppetserver.service
    SHELL
  end

  config.vm.define "starcraft_fe_01" do |starcraft_fe_01|
    starcraft_fe_01.vm.network "public_network", ip: "192.168.1.101", bridge: "enp4s0f2np2"
    starcraft_fe_01.vm.hostname = "starcraft-fe-01"
    puppet_agent(starcraft_fe_01)
  end

  config.vm.define "starcraft_fe_02" do |starcraft_fe_02|
    puppet_agent(starcraft_fe_02)
    starcraft_fe_02.vm.network "public_network", ip: "192.168.1.102", bridge: "enp4s0f2np2"
    starcraft_fe_02.vm.hostname = "starcraft-fe-02"
  end

  config.vm.define "starcraft_be_01" do |starcraft_be_01|
    puppet_agent(starcraft_be_01)
    starcraft_be_01.vm.network "public_network", ip: "192.168.1.150", bridge:"enp4s0f2np2"
    starcraft_be_01.vm.hostname = "starcraft-be-01"
  end
end

