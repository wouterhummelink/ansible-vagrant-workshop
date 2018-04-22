# vim ft=ruby
groups = {
  "webservers" => ["web"],
  "appservers" => ["app"],
  "dbservers" => ["db"]
}

extra_vars = {
  "ansible_user" => "vagrant",
  "ansible_password" => "vagrant",
  "host_key_checking" => "False"
}

host_vars = {
  "control" => { "ansible_connection" => "local" },
  "web" => { "ansible_host" => "192.168.33.11" },
  "app" => { "ansible_host" => "192.168.33.12" },
  "db" => { "ansible_host" => "192.168.33.13" }
}

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"
  config.vm.provision "shell", inline: "sed -i 's/PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config && systemctl restart sshd"
  config.vm.define "web" do |web|
    web.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end
    web.vm.network "private_network", ip: "192.168.33.11"
  end
  config.vm.define "app" do |app|
    app.vm.provider "virtualbox" do |v|
      v.memory = 1024
      v.cpus = 1
    end
    app.vm.network "private_network", ip: "192.168.33.12"
  end
  config.vm.define "db" do |db|
    db.vm.provider "virtualbox" do |v|
      v.memory = 1024
      v.cpus = 1
    end
    db.vm.network "private_network", ip: "192.168.33.13"
  end
  config.vm.define "control" do |control|
    control.vm.network "private_network", ip: "192.168.33.10"
    control.vm.synced_folder ".", "/vagrant", type: "rsync", rsync_exclude: ["*.swp"]
    control.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "playbook.yml"
      ansible.provisioning_path = "/vagrant"
      ansible.limit = "all"
      ansible.groups = groups
      ansible.host_vars = host_vars
      ansible.extra_vars = extra_vars
    end
  end
end

