Vagrant.configure('2') do |config|
  numberOfNodes=4

  kubelets = Array.new(numberOfNodes)
  (1..numberOfNodes).each do |id|
      kubelets[id - 1] = "kube#{id}"
  end

  config.vm.box = "centos/7"
  config.vm.network "private_network", type: "dhcp"

  config.vm.provider 'virtualbox' do |vb|
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
      vb.memory = '4098'
  end
  
  (1..numberOfNodes).each do |host|
      config.vm.define "kube#{host}" do |server|
          if host == 1 then
            server.vm.network "forwarded_port", guest: 16686, host: 16686, guest_ip: "127.0.0.1" #Jaeger
            server.vm.network "forwarded_port", guest: 3000, host: 3000, guest_ip: "127.0.0.1" #graphana
            server.vm.network "forwarded_port", guest: 9090, host: 9090, guest_ip: "127.0.0.1" #Prometheus
          end
      
          server.vm.hostname = "kube#{host}"
          server.vm.synced_folder '.', '/vagrant', type: 'virtualbox'
          server.vm.network "private_network", ip: "172.18.10.8#{host}"
          server.vm.provision "ansible" do |ansible|
              #ansible.playbook = 'playbooks/playbook.yml'
              ansible.playbook = 'install.yml'
          end
      end
  end
end