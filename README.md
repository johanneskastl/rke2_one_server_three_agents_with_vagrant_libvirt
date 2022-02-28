# rke2_one_server_three_agents_with_vagrant_libvirt

Vagrant setup for building a RKE cluster with one controlplane node and three agents, using the vagrant libvirt provider 

This setup creates one controlplane VM and three agent VMs.

Default OS is openSUSE Leap 15.3, but that can be changed in the Vagrantfile. Same holds true for the sizing of the machines.

## Vagrant

1. You need vagrant obviously. And libvirt. And ansible.
2. Fetch the box, per default this is `opensuse/Leap-15.3.x86_64`, using `vagrant box add opensuse/Leap-15.3.x86_64`.
3. Make sure the machine, where you want to run this on, has enough RAM to allow running the VMs. If not, adjust the sizes in the `Vagrantfile`.
4. Run `vagrant up`
5. Run `kubectl --kubeconfig ansible/rke2-kubeconfig get nodes` and you should see your one control-plane node and three agent nodes.
6. Party!

## Adjusting VM sizes in the Vagrantfile

In case you want or need to change the VM sizing, change the following lines in the `Vagrantfile`:

For the server:
```
      # sizing of the server VM
      node.vm.provider "libvirt" do |lv|
        lv.memory = 4096
        lv.cpus = 2
      end
```

For the agents:
```
      # sizing of the agent VMs
      node.vm.provider "libvirt" do |lv|
        lv.memory = 2048
        lv.cpus = 2
      end
```

## Increasing or decreasing the number of agent nodes

In case you want to increase the number of agent nodes, you need to adjust two lines in the `Vagrantfile`:

Change the number in this line:
```
  ###################################################################################
  # define number of agents
  W = 1
```

Then add the additional hostnames to the `rke2_agents` list (or remove the ones you do no longer need):
```
        ansible.groups = {
            "rke2_servers"  => [ "rke2-server1" ],
            "rke2_agents"  => [ "rke2-agent1", "rke2-agent2", "rke2-agent3" ],
        }
```

## Increasing the number of server nodes

In case you want to have more than one server node, there are several things that you need to do:
1. adjust the number of server nodes in the `Vagrantfile`
2. adjust the ansible `rke2_servers` group in the `Vagrantfile` and add the additional server names
3. adjust the ansible playbook `playbook-vagrant.yml`

### ad 1: number of servers

Change the following line in the `Vagrantfile`
```
  # define number of servers
  M = 1
```

### ad 2: ansible group `rke2_servers`

Add the additional servers to the `rke2_servers` group:
```
[...]
          ansible.groups = {
            "rke2_servers"  => [ "rke2-server1", "rke2-server2", "rke2-server3", ... ],
[...]
```

### ad 3: playbook adaption

You might want to take a look at the [`playbook-vagrant.yml` playbook](https://github.com/johanneskastl/rke2_three_servers_with_vagrant_libvirt/blob/main/ansible/playbook-vagrant.yml) in the Vagrant setup with three server nodes:
https://github.com/johanneskastl/rke2_three_servers_with_vagrant_libvirt

Basically this installs the first rke2 server and then adds one node at a time.

## Cleaning up

You can remove your vagrant VMs using `vagrant destroy`, but you need to manually remove the following files before starting again:
```bash
ansible/rke2-kubeconfig
```
