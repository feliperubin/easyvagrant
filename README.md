# Easy Vagrant for Elaborate Environments

No ruby needed. Define VMs with additional disks, networks, replicas and scripts using a single yaml file.

**Supported Provisioners**
- VirtualBox
- Parallels Desktop
- VMware Fusion

It should be pretty straightforward to implement new providers, with the exception of creating extra disks.

**Features**

- *Provisioning*
  > Use the same or choose a different provider for every instance, select headless mode or GUI, use linked clone for reduced storage space, enable x11 for ssh connections, specify a hostname and a label to identify the VM manually.
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
