# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "almalinux/9"
  config.vm.box_version = "9.3.20231118"

  # config.vm.network "private_network", type: "dhcp"
  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
    vb.memory = "2048"
  end

  # カレントディレクトリを /vagrant にマウント
  config.vm.synced_folder ".", "/vagrant"

  # ホストの公開鍵を /tmp に転送
  host_pub_key_path = "#{ENV['USERPROFILE'].gsub('\\', '/')}/.ssh/id_ed25519.pub"
  config.vm.provision "file", source: host_pub_key_path, destination: "/tmp/host_id_ed25519.pub"

  config.vm.provision "shell", inline: <<-SHELL
    # 日本語化
    dnf -y install langpacks-ja
    localectl set-locale LANG=ja_JP.UTF-8
    source /etc/locale.conf
    timedatectl set-timezone Asia/Tokyo

    # SSH: ホストの公開鍵を追加
    mkdir -p /home/vagrant/.ssh
    cat /tmp/host_id_ed25519.pub >> /home/vagrant/.ssh/authorized_keys
    chmod 700 /home/vagrant/.ssh
    chmod 600 /home/vagrant/.ssh/authorized_keys
    chown -R vagrant:vagrant /home/vagrant/.ssh
    rm /tmp/host_id_rsa.pub

    # Docker のインストール
    dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    dnf -y install docker-ce
    usermod -aG docker vagrant
    systemctl enable --now docker
  SHELL

end
