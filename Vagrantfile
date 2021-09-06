WORKER_NODES_NUMBER = 1
IMAGE_NAME = "generic/ubuntu1804"


Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
    
  config.vm.provider "libvirt" do |v|
    v.memory = 8192
    v.cpus = 2
# next line is required so that you can use libvirt as normal user
    v.qemu_use_session = false
  end
  
  # k8s cluster setup

  # k8s master setup
  config.vm.define "master" do |master|
    
    master.vm.box = IMAGE_NAME
    master.vm.hostname = "master"
    master.vm.network "private_network", ip: "192.168.122.10", libvirt__network_name: "default", libvirt__host_ip: "192.168.122.1"

    master.vm.provision "ansible" do |ansible|
      ansible.playbook = "master-playbook.yaml"
      ansible.extra_vars = { node_ip: "192.168.122.10", }
    end
  end

  # k8s worker setup
  (1..WORKER_NODES_NUMBER).each do |i|
    config.vm.define "worker-#{i}" do |node|

      node.vm.box = IMAGE_NAME
      node.vm.hostname = "worker-#{i}"
      node.vm.network "private_network", ip: "192.168.122.#{i + 10}", libvirt__network_name: "default", libvirt__host_ip: "192.168.122.1"

      node.vm.provision "ansible" do |ansible|
        ansible.playbook = "node-playbook.yaml"
        ansible.extra_vars = { node_ip: "192.168.122.#{i + 10}",
                               kubelet_file: "worker-#{i}.conf"}
      end
    end
  end
#
end