Vagrant.configure(2) do |config|
  config.vm.box = "fedora/26-cloud-base"
  config.ssh.insert_key = false
  config.vm.hostname = "fvagrant"
  config.vm.define :vagrant_module_tester do |vagrant_host|
    vagrant_host.vm.provider :libvirt do |domain|
      #domain.cpus = 1
      #domain.memory = 16384
      domain.memory = 1024
      domain.nested = true
      #domain.cpu_mode = "host-model" #having this flag set makes the vm not launch for me --langdon
    end

    vagrant_host.vm.provision "shell", inline: "sudo dnf -y update"
    vagrant_host.vm.provision "shell", inline: "sudo dnf -y install git make pkgwat"
    #make sure we stick to py2 for everything
    vagrant_host.vm.provision "shell", inline: "sudo dnf install -y --enablerepo=updates-testing module-build-service python2-modulemd python2-pdc-client mock-scm"
    #add docker for using the boltron-preview to test
    vagrant_host.vm.provision "shell", inline: "sudo dnf install -y docker atomic"
    vagrant_host.vm.provision 'shell', inline: "sudo systemctl stop docker > /dev/null 2>&1 || :" #in case this isn't first run
    vagrant_host.vm.provision 'shell', inline: "sudo groupadd docker > /dev/null 2>&1 || :" #in case this isn't first run
    vagrant_host.vm.provision 'shell', inline: "sudo usermod -a -G docker vagrant"
    vagrant_host.vm.provision 'shell', inline: "sudo systemctl enable docker && sudo systemctl start docker"
    vagrant_host.vm.provision 'shell', inline: "sudo chown root:docker /var/run/docker.sock"
    vagrant_host.vm.provision 'shell', inline: "sudo docker pull modularitycontainers/boltron-preview"

    vagrant_host.vm.provision "shell", inline: "sudo usermod -a -G mock vagrant"
    vagrant_host.vm.provision "shell", inline: "sudo dnf install --allowerasing -y https://kojipkgs.fedoraproject.org//packages/mock/1.3.4/1.fc26/noarch/mock-1.3.4-1.fc26.noarch.rpm"
    vagrant_host.vm.provision "shell", inline: "sudo dnf install --allowerasing -y https://kojipkgs.fedoraproject.org//packages/mock/1.3.4/1.fc26/noarch/mock-lvm-1.3.4-1.fc26.noarch.rpm"
    vagrant_host.vm.provision "shell", inline: "sudo dnf install --allowerasing -y https://kojipkgs.fedoraproject.org//packages/mock/1.3.4/1.fc26/noarch/mock-scm-1.3.4-1.fc26.noarch.rpm"

    #enable depchase and other modularity tools
    vagrant_host.vm.provision "shell", inline: "sudo dnf -y install python3-solv python3-click fedora-packager"
    vagrant_host.vm.provision "shell", inline: "mkdir ~/modularity-tools/ > /dev/null 2>&1 || :" #in case this isn't first run
    vagrant_host.vm.provision "shell", inline: "git clone https://github.com/fedora-modularity/baseruntime-package-lists.git ~/modularity-tools/baseruntime-package-lists > /dev/null 2>&1 || :" #in case this isn't first run
    vagrant_host.vm.provision "shell", inline: "git clone https://github.com/fedora-modularity/depchase.git ~/modularity-tools/depchase > /dev/null 2>&1 || :" #in case this isn't first run
    vagrant_host.vm.provision "shell", inline: "cd ~/modularity-tools/depchase && git checkout verbose"
    
  end
end
