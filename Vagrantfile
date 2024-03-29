Vagrant.configure("2") do |config|
  config.vm.base_mac = nil
  config.vm.synced_folder ".", "/vagrant", disabled: false

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "2048"
    vb.cpus = 2
    vb.linked_clone = true
  end

  ## additional vm for network hacking imitation if u need it
  # config.vm.define "hacker" do |n|
  #   n.vm.provider "virtualbox" do |r|
  #     r.memory = "1024"
  #     r.cpus = 1
  #   end
  # end

  config.vm.define "master" do |n|
    n.vm.provider "virtualbox" do |r|
      r.memory = "2048"
      r.cpus = 2
    end
    n.vm.box = "ubuntu/bionic64"
    n.vm.hostname = "master"
    n.vm.network "private_network", ip: "192.168.56.10"
    n.vm.network "forwarded_port", guest: 1230, host: 1230, guest_ip: "192.168.56.10"
    n.vm.network "forwarded_port", guest: 1231, host: 1231, guest_ip: "192.168.56.10"
    n.vm.network "forwarded_port", guest: 1232, host: 1232, guest_ip: "192.168.56.10"
    n.vm.network "forwarded_port", guest: 1233, host: 1233, guest_ip: "192.168.56.10"
    n.vm.network "forwarded_port", guest: 1234, host: 1234, guest_ip: "192.168.56.10"
    n.vm.network "forwarded_port", guest: 1235, host: 1235, guest_ip: "192.168.56.10"
    n.vm.network "forwarded_port", guest: 1236, host: 1236, guest_ip: "192.168.56.10"
  end

  N = 2
  (1..N).each do |machine_id|
    config.vm.define "node-#{machine_id}" do |n|
      n.vm.hostname = "node-#{machine_id}"
      n.vm.network "private_network", ip: "192.168.56.#{20+machine_id}"
      n.vm.box = "ubuntu/bionic64"

      if machine_id == N
        n.vm.provision :ansible do |ansible|
          ansible.limit = "all"
          ansible.playbook = "site.yaml"
          ansible.groups = {
            "kubernetes" => [ "master", "node-[1:#{N}]" ],
            "kubernetes-master" => [ "master" ],
            "kubernetes-node" => [ "node-[1:#{N}]" ],
            "all:vars" => { "kubernetes_default_interface" => "enp0s8",
                            "kubernetes_base_ip" => "{{ hostvars[inventory_hostname]['ansible_'+kubernetes_default_interface]['ipv4']['address'] }}",
                            "kubernetes_master_pod_subnet" =>  "172.16.0.0/16",
                            "kubernetes_repo_version" =>  "1.19.7-*",
                            "kubernetes_cni_version" =>  "0.8.*" }
          }
          ansible.raw_arguments = [ "-D" ]
        end
      end
    end
  end
end
