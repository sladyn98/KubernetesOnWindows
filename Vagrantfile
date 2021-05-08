Vagrant.configure(2) do |config|
  config.vagrant.plugins = ["vagrant-reload"]
  config.vm.define :main do |main|
    main.vm.host_name = "main"
    main.vm.box = "hashicorp/bionic64"
    main.vm.network :private_network, ip:"10.20.30.10"
    main.vm.synced_folder "./sync", "/var/sync", smb_username: ENV['SMB_USERNAME'], smb_password: ENV['SMB_PASSWORD']
    main.vm.provider :virtualbox do |vb|
      vb.memory = 4096
      vb.cpus = 2
    end
    main.vm.provider :hyperv do |hv|
      hv.memory = 4096
      hv.cpus = 2
    end
    main.vm.provision :shell, privileged: false, path: "sync/main.sh"
  end

  config.vm.define :winworker do |winworker|
    winworker.vm.host_name = "winworker"
    winworker.vm.box = "StefanScherer/windows_2019"     
    winworker.vm.provider :virtualbox do |vb|
      vb.memory = 2048
      vb.cpus = 2
      vb.gui = true
    end 
    winworker.vm.provider :hyperv do |hv|
      hv.enable_virtualization_extensions = true      
      hv.memory = 2048
      hv.cpus = 2
    end

    winworker.vm.synced_folder "./sync", "c:\\sync", smb_username: ENV['SMB_USERNAME'], smb_password: ENV['SMB_PASSWORD']

    winworker.vm.provision "shell", path: "sync/hyperv.ps1", privileged: true
    winworker.vm.provision :reload
    winworker.vm.provision "shell", path: "https://github.com/kubernetes-sigs/sig-windows-tools/releases/latest/download//Install-Containerd.ps1", privileged: true, args: "-ContainerDVersion 1.4.4"
    winworker.vm.provision :reload
    winworker.vm.provision "shell", path: "https://github.com/kubernetes-sigs/sig-windows-tools/releases/latest/download//Install-Containerd.ps1", privileged: true, args: "-ContainerDVersion 1.4.4"
    winworker.vm.provision "shell", path: "https://github.com/kubernetes-sigs/sig-windows-tools/releases/latest/download/PrepareNode.ps1", privileged: true, args: "-KubernetesVersion v1.21.0 -ContainerRuntime containerD"
    winworker.vm.provision "shell", path: "sync/prepjoin.ps1", privileged: true
    winworker.vm.provision "shell", path: "sync/kubejoin.ps1", privileged: true
  end
  
end