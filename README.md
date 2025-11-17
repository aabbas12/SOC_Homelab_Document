<img width="2024" height="1355" alt="Screenshot_20251117_133815" src="https://github.com/user-attachments/assets/ea110c53-00c1-4266-bf9b-7f142b049c72" /># SOC_Homelab_Document

# Setting up the Virtual Machines

## Setting Up The Windows 10 Victim Machine

Using the Windows 10 Installation Media Creation Tool, created a Windows 10 ISO file

Set up the virtual machine in Oracle VMBox, walked through the installer

<img width="968" height="937" alt="Screenshot_20251115_160318" src="https://github.com/user-attachments/assets/34e0a816-4f4b-4790-b7ef-4e48b2a54f7c" />


## Setting up the Kali Machine


## Configuring the network
I then set up a new isolated network within KVM. This allows me to use my VMs on an network that is logically seperate from my host machine and the internet. I did this for safety to
minimize the risk of any unwanted traffic traversing outside of the virtuals machines.

<img width="1404" height="1067" alt="Screenshot_20251115_190221" src="https://github.com/user-attachments/assets/ba813885-55f9-4580-8b55-b4fd1f2ae879" />


I then configured each of the virtual machines to be attatched to this network. I'm using virtio as the network interface, since it's more CPU effecient than the alternatives, though it does
take a little more effort to set up (Namely in setting up the drivers necessary to make it work).

### Ensusing Driver compatability
The additional steps I took to set up virtio to work properly with my hypervisor were...
- For Windows...
-   Installing the virtio.win iso drivers from the official fedora linux repositories
-   Moving it to a suitable directory on my host
-   Attatching the drivers' iso file to the windows VM's SATA CDROM
-   Downloading the drivers to the windows VM itself
-  For Kali...
-    No additional configuration needed since it comes with all the necessary prerequisite drivers installed

<img width="1179" height="842" alt="Screenshot_20251115_193423" src="https://github.com/user-attachments/assets/d2f6e5a6-26cc-416f-93ea-2839a93d33f1" />

<img width="1997" height="1352" alt="Screenshot_20251116_144028" src="https://github.com/user-attachments/assets/6bd661a5-128f-41a8-ac6b-67b2ecc69944" />


### Assigning IPs
Next, since I'm using these machines on an isolated network within KVM and they're only going to need to talk to each other, I got started with statically setting up each of their IP addresses with IPs that belong to private non routable subnets.

For the windows machine, I configured the following under the network adapter's properties

```
IP: 10.10.0.10
Subnet Mask: 255.255.255.0
```

<img width="2024" height="1355" alt="Screenshot_20251117_133815" src="https://github.com/user-attachments/assets/fcc447f5-e9f3-46e9-b275-e1530428a74d" />

(Verifying to ensure that the change went through)
<img width="2024" height="1355" alt="Screenshot_20251117_134222" src="https://github.com/user-attachments/assets/82b32e98-97d2-4d14-81e3-602443c05234" />

As for the Kali machine, I configured the following under the network properties

```
IP: 10.10.0.11
Subnet Mask: 255.255.255.0
```

<img width="2011" height="1401" alt="Screenshot_20251117_134523" src="https://github.com/user-attachments/assets/0f520051-6f7b-4e3f-b8b5-c38cb511ed8d" />

