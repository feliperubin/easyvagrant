# Easy Vagrant for Elaborate Environments

No ruby needed. Define VMs with additional disks, networks, replicas and scripts using a single yaml file.

**Supported Provisioners**

It should be pretty straightforward to implement new providers, with the exception of creating extra disks as it depends on the provider's API. The following providers are implemented at the moment:

- VirtualBox
- VMware Fusion
- Parallels Desktop


**Features**

- *Provisioning*
  > Select headless mode or GUI, use linked clone for reduced storage space, enable x11 for ssh connections, specify a hostname and a label to identify the VM manually.
- *Hardware*
  > Define from simple vCPU and RAM to nested virtualization support, extra disks and network interfaces, enable 3d video and specify the amount of vram.
- *Network*
  > Use dhcp or static addresses in public, private, and host-only networks, or add interfaces for later configuration using bridges based on physical adapters.
- *Replicas*
  > Create multiple instances with the same configurations. Automatically adjust network addresses and use a special argument ($$) to differentiate each instance when sharing files and directories or when running commands or scripts or directories.
- *Disks*
  > Specify extra raw disks and their corresponding sizes.
- *Scripts*
  > Run script files or single commands with arguments during provisioning in privileged mode or not.
- *Shares*
  > Synchronise or copy directories and (copy) files to instances, specifying ownership, permissions, location, and mount options.


### Example

This creates an environment with 3 virtual machines: A master with Ubuntu 18.04 LTS and IP Address 10.0.0.2 for a host-only private network, two additional nodes replicas node-100 with IP Address 10.0.0.3 and node-101 with the next address (10.0.0.4). The `$$` is replaced by 0 if no `replicaid` was specified , or the corresponding `replicaid` (incremented by 1 for each replica).

Note: The yaml file **must** be named `hosts.yml`.

**Create Environment**
```sh
vagrant up
# OR Specifying the provider
vagrant up --provider=[vmware_fusion|parallels|virtualbox]
```

**Destroy Environment**
```sh
vagrant destroy -f
```



```yaml
- name: master
  box: bento/ubuntu-18.04
  mem: 2048
  cpu: 2
  linkedclone: false
  nestedvt: true
  network:
    - kind: private_network
      ip: 10.0.0.2
      mask: 255.255.255.0
    - kind: public_network # NIC:eth2, Provider Network
      bridge: en0 # Ethernet Card
      auto_config: false
    - path: write_to_file.sh
      args: [/home/vagrant/i_am_again_$$.txt,"$$"] # Replaced by replicaid, defaults to 0
      privileged: false

# This creates two nodes: node-100 10.0.0.3 , node-101 10.0.0.4
- name: "node-" #
  box: bento/ubuntu-20.04
  mem: 1024
  cpu: 2
  linkedclone: true
  nestedvt: false
  namelabel: true
  x11: true
  gui: false
  vram: 256
  replicas: 2
  replicaid: 100
  video3d: false
  network:
    - kind: private_network # NIC:eth1, Management Network (Host Only)  NIC: eth1
      ip: 10.0.0.3
      mask: 255.255.255.0
    - kind: public_network # NIC:eth2, Provider Network
      bridge: en0 # Ethernet Card
      auto_config: false
  disk:
    - size: 50 # Extra disk 1 (size in GB)
    - size: 25 # Extra disk 2 , 25GB
  script:
    - cmd: "echo 'My replicaid is $$' > /home/vagrant/hello_$$.txt"
      privileged: false
    - path: write_to_file.sh
      args: "$$ /home/vagrant/i_am_$$.txt" 
      privileged: true
  share:
    - source: "./node-$$"
      disabled: false
      target: "/mnt/share"
      create: true
      owner: "vagrant"
      group: "vagrant"
      sync: true
    - source: "dataset.csv"
      target: "/home/vagrant/dataset.csv"
      options: ["uid=501", "gid=1000"]
      sync: false
    - source: "./project/src"
      target: "/home/vagrant/code"
      sync: false
```
