Vagrant.configure("2") do |config|
  # SSH behavior
  config.ssh.forward_agent    = true
  config.ssh.insert_key       = false
  config.ssh.private_key_path = [
    "~/.vagrant.d/insecure_private_key",
    "~/.ssh/vagrant"
  ]

  # =========================
  # Kali Linux VM (htb)
  # =========================
  config.vm.define "htb" do |htb|
    htb.vm.box = "kalilinux/rolling"
    htb.vm.hostname = "htb"

    # Bridged network (CHANGE bridge if needed)
    htb.vm.network "public_network",
      ip: "192.168.1.40",
      bridge: "eno1"

    # RDP forward (guest 3389 -> host 3391)
    htb.vm.network "forwarded_port",
      guest: 3389,
      host: 3391

    htb.vm.provider "virtualbox" do |v|
      v.memory = 8192
      v.cpus   = 4
      v.gui    = false
    end
  end

  # =========================
  # Windows 10 VM (sb)
  # =========================
  config.vm.define "sb" do |sb|
    sb.vm.box = "gusztavvargadr/windows-10"
    sb.vm.hostname = "stealth"

    # Bridged DHCP (CHANGE bridge if needed)
    sb.vm.network "public_network",
      type: "dhcp",
      bridge: "eno1",
      use_dhcp_assigned_default_route: true

    # RDP forward (guest 3389 -> host 3390)
    sb.vm.network "forwarded_port",
      guest: 3389,
      host: 3390

    sb.vm.provider "virtualbox" do |vb|
      vb.memory = 4096
      vb.cpus   = 2
      vb.name   = "StealthVM"

      vb.customize ["modifyvm", :id, "--acpi", "on"]
      vb.customize ["modifyvm", :id, "--paravirtprovider", "default"]
      vb.customize ["setextradata", :id, "VBoxInternal2/IdleShutdownTimeout", "0"]
    end
  end
end

