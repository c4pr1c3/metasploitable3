# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.synced_folder '.', '/vagrant', disabled: true
  config.vm.define "ub1404" do |ub1404|
    ub1404.vm.box = "rapid7/metasploitable3-ub1404"
    ub1404.vm.hostname = "metasploitable3-ub1404"
    config.ssh.username = 'vagrant'
    config.ssh.password = 'vagrant'

    ub1404.vm.network "private_network", ip: '172.28.128.3'

    ub1404.vm.provider "virtualbox" do |v|
      v.name = "Metasploitable3-ub1404"
      v.memory = 2048
    end
  end

  config.vm.define "win2k8" do |win2k8|
    # Base configuration for the VM and provisioner
    win2k8.vm.box = "rapid7/metasploitable3-win2k8"
    # 访问 https://app.vagrantup.com/rapid7/boxes/metasploitable3-win2k8 获取到 virtualbox 镜像的下载 url
    # https://app.vagrantup.com/rapid7/boxes/metasploitable3-win2k8/versions/0.1.0-weekly/providers/virtualbox.box
    # 使用浏览器访问后获得「动态生成的」真实下载地址
    # 真实下载地址放到下载工具加速下载
    # 重命名下载完成的文件名为 win2k8.box
    win2k8.vm.box_url = "win2k8.box"
    win2k8.vm.hostname = "metasploitable3-win2k8"
    win2k8.vm.communicator = "winrm"
    win2k8.winrm.retry_limit = 60
    win2k8.winrm.retry_delay = 10

    win2k8.vm.network "private_network", type: "dhcp"

    win2k8.vm.provider "libvirt" do |v|
      v.memory = 4096
      v.cpus = 2
      v.video_type = 'qxl'
      v.input :type => "tablet", :bus => "usb"
      v.channel :type => 'unix', :target_name => 'org.qemu.guest_agent.0', :target_type => 'virtio'
      v.channel :type => 'spicevmc', :target_name => 'com.redhat.spice.0', :target_type => 'virtio'
      v.graphics_type = "spice"

      # Enable Hyper-V enlightenments: https://blog.wikichoon.com/2014/07/enabling-hyper-v-enlightenments-with-kvm.html
      v.hyperv_feature :name => 'stimer',  :state => 'on'
      v.hyperv_feature :name => 'relaxed', :state => 'on'
      v.hyperv_feature :name => 'vapic',   :state => 'on'
      v.hyperv_feature :name => 'synic',   :state => 'on'
    end

    # Configure Firewall to open up vulnerable services
    case ENV['MS3_DIFFICULTY']
      when 'easy'
        win2k8.vm.provision :shell, inline: "C:\\startup\\disable_firewall.bat"
      else
        win2k8.vm.provision :shell, inline: "C:\\startup\\enable_firewall.bat"
        win2k8.vm.provision :shell, inline: "C:\\startup\\configure_firewall.bat"
    end

    # Insecure share from the Linux machine
    win2k8.vm.provision :shell, inline: "C:\\startup\\install_share_autorun.bat"
    win2k8.vm.provision :shell, inline: "C:\\startup\\setup_linux_share.bat"
    win2k8.vm.provision :shell, inline: "rm C:\\startup\\*" # Cleanup startup scripts
  end
end
