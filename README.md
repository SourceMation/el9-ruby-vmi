# EuroLinux 9 Ruby 3 VMI
This repo contains virtual machine images and Jenkins pipeline to build them.  
We offer VMware, VirtualBox and Qemu VMI for your convenience.  
All images are built once a month using packer(packer.io) and additional scripts.

## Installation  
### VMware
1.No need to download.  
2.Launch the vsphere UI.  
3.Select 'Deploy OVF Template' and provide URL to the *vmware.ova file.  
4.Go through the virtual machine wizard according to your preferences and confirm its creation.  
5.Start the virtual machine.  
### Virtualbox
1.Download the latest release of *vbox.ova file.  
2.Launch the virtualbox UI.  
3.Select 'Import Appliance' and specify path to the ova file.  
4.Go through the virtual machine wizard according to your preferences and confirm its creation.  
5.Start the virtual machine.  
### Qemu
1.Download the latest release of *qemu.qcow2 file.  
2.Launch the virt-manager.  
3.Select 'New virtual machine' then 'Import existing disk image' and speciy path to the file.  
4.Go through the virtual machine wizard according to your preferences and confirm its creation.  
5.Start the virtual machine.  

## License
This image uses [GPLv2](https://github.com/EuroLinux/eurolinux-9-el-release/blob/master/GPL),
and some other open source licences that are included directly in the image.