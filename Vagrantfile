BOX_IMAGE = "bento/ubuntu-18.04"

Vagrant.configure("2") do |config|

  config.vm.define "vault-server" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "vault"
    subconfig.vm.network "private_network", ip: "192.168.56.10"
    subconfig.vm.provider :virtualbox do |vb|
             vb.customize ['modifyvm', :id,'--memory', '512']
    end
    subconfig.vm.provision "shell" do |s|
      s.path = "install-vault.sh"
      s.args = ["192.168.56.10","server"]
    end
    subconfig.vm.provision "shell" do |s|
      s.path =  "install-consul.sh"
      s.args = ["192.168.56.10", '"192.168.56.10","192.168.56.11","192.168.56.12","192.168.56.13","192.168.56.14","192.168.56.15"', 'dc1', 0, "false"]
    end
  end

  config.vm.define "consul-1-server" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "consul-1"
    subconfig.vm.network "private_network", ip: "192.168.56.11"
    subconfig.vm.provider :virtualbox do |vb|
             vb.customize ['modifyvm', :id,'--memory', '512']
    end
    subconfig.vm.provision "shell" do |s|
      s.path =  "install-consul.sh"
      s.args = ["192.168.56.11", '"192.168.56.10","192.168.56.11","192.168.56.12","192.168.56.13","192.168.56.14","192.168.56.15"', 'dc1', 3, "true"]
    end
    subconfig.vm.network "forwarded_port", guest: 7500, host: 57500
  end

  config.vm.define "consul-2-server" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "consul-2"
    subconfig.vm.network "private_network", ip: "192.168.56.12"
    subconfig.vm.provider :virtualbox do |vb|
             vb.customize ['modifyvm', :id,'--memory', '512']
    end
    subconfig.vm.provision "shell" do |s|
      s.path =  "install-consul.sh"
      s.args = ["192.168.56.12", '"192.168.56.10","192.168.56.11","192.168.56.12","192.168.56.13","192.168.56.14","192.168.56.15"', 'dc1', 0, "true"]
    end
  end

  config.vm.define "consul-3-server" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "consul-3"
    subconfig.vm.network "private_network", ip: "192.168.56.13"
    subconfig.vm.provider :virtualbox do |vb|
             vb.customize ['modifyvm', :id,'--memory', '512']
    end
    subconfig.vm.provision "shell" do |s|
      s.path =  "install-consul.sh"
      s.args = ["192.168.56.13", '"192.168.56.10","192.168.56.11","192.168.56.12","192.168.56.13","192.168.56.14","192.168.56.15"', 'dc1', 0, "true"]
    end
  end

  system("
    if [ #{ARGV[0]} = 'up' ]; then
        echo \"Generating SSH Keys needed for Jenkins and storing in $(pwd)\"
        ./create-jenkins-keys.sh
    fi
  ")

  config.vm.define "jenkins-master" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "jenkins"
    subconfig.vm.network "private_network", ip: "192.168.56.14"
    subconfig.vm.provider :virtualbox do |vb|
          vb.customize ['modifyvm', :id,'--memory', '512']
    end
    subconfig.vm.provision "shell" do |s|
      s.path = "install-jenkins-master.sh"
      s.args = ["192.168.56.15"]
    end
    subconfig.vm.provision "shell" do |s|
      s.path = "install-vault-agent-master.sh"
      s.args = ["192.168.56.14","192.168.56.10"]
    end
    subconfig.vm.provision "shell" do |s|
      s.path =  "install-consul.sh"
      s.args = ["192.168.56.14", '"192.168.56.10","192.168.56.11","192.168.56.12","192.168.56.13","192.168.56.14","192.168.56.15"', 'dc1', 0, "false"]
    end
  end

  config.vm.define "jenkins-worker" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "jenkins"
    subconfig.vm.network "private_network", ip: "192.168.56.15"
    subconfig.vm.provider :virtualbox do |vb|
            vb.customize ['modifyvm', :id,'--memory', '512']
    end
    subconfig.vm.provision "shell" do |s|
     s.path = "install-jenkins-worker.sh"
     s.args = ["192.168.56.14"]
    end
    subconfig.vm.provision "shell" do |s|
      s.path = "install-vault-agent-worker.sh"
      s.args = ["192.168.56.15","192.168.56.10"]
    end
    subconfig.vm.provision "shell" do |s|
      s.path =  "install-consul.sh"
      s.args = ["192.168.56.15", '"192.168.56.10","192.168.56.11","192.168.56.12","192.168.56.13","192.168.56.14","192.168.56.15"', 'dc1', 0, "false"]
    end
  end
end
