# SOC_Homelab_Document

Setting Up The Windows 10 Victim Machine
Using the Windows 10 Installation Media Creation Tool, created a Windows 10 ISO file
Set up the virtual machine in Oracle VMBox, walked through the installer

<img width="543" height="486" alt="windows setup" src="https://github.com/user-attachments/assets/da381259-58b4-40e2-bb3c-2437b28cb183" />


I then set up a new isolated network within KVM. This allows me to use my VMs on an network that is logically seperate from my host machine and the internet. I did this for safety to
minimize the risk of any unwanted traffic traversing outside of the virtuals machines.

<img width="1404" height="1067" alt="Screenshot_20251115_190221" src="https://github.com/user-attachments/assets/ba813885-55f9-4580-8b55-b4fd1f2ae879" />


I then configured each of the virtual machines to be attatched to this network. I'm using virtio as the network interface, since it's more CPU effecient than the alternatives, though it does
take a little more effort to set up (Namely in setting up the drivers necessary to make it work).

The additional steps I took to set up virtio to work properly with my hypervisor were...
- Installing the virtio.win iso drivers from the official fedora linux repositories
-  Moving it to a suitable directory on my host
-  Attatching the drivers' iso file to the windows VM's SATA CDROM
-  
<img width="1910" height="531" alt="Screenshot_20251115_192849" src="https://github.com/user-attachments/assets/d0b9c2ef-05ad-4c34-a56f-ee7ea4cc41b4" />
