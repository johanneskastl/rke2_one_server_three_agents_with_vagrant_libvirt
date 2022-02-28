Vagrant.configure("2") do |config|

  ###################################################################################
  # define number of agents
  W = 3

  # provision W VMs as agents
  (1..W).each do |i|

    # name the VMs
    config.vm.define "rke2-agent#{i}" do |node|

      # which image to use
      node.vm.box = "opensuse/Leap-15.3.x86_64"

      # sizing of the agent VMs
      node.vm.provider "libvirt" do |lv|
        lv.memory = 2048
        lv.cpus = 2
      end

      # set the hostname
      node.vm.hostname = "rke2-agent#{i}"

    end # config.vm.define agents

  end # each-loop agents
  ###############################################

  # define number of servers
  M = 1

  # provision M VMs as servers
  (1..M).each do |i|

    # name the VMs
    config.vm.define "rke2-server#{i}" do |node|

      # which image to use
      node.vm.box = "opensuse/Leap-15.3.x86_64"

      # sizing of the server VM
      node.vm.provider "libvirt" do |lv|
        lv.memory = 4096
        lv.cpus = 2
      end

      # set the hostname
      node.vm.hostname = "rke2-server#{i}"

      # if this is the last machine
      if i == M

        node.vm.provision "ansible" do |ansible|
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
          ansible.groups = {
            "rke2_servers"  => [ "rke2-server1" ],
            "rke2_agents"  => [ "rke2-agent1", "rke2-agent2", "rke2-agent3" ],
          }
          ansible.playbook = "ansible/playbook-vagrant.yml"
        end # node.vm.provision

      end # if-condition last machine

    end # config.vm.define servers

  end # each-loop servers

end # Vagrant.configure
