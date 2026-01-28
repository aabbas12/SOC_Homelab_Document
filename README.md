# Setting up the Virtual Machines


## Configuring the network
To start, I set up a new isolated network within KVM. This will allow me to use my VMs on an network that is logically seperate from my host machine and the internet. I did this for safety to
minimize the risk of any unwanted traffic traversing outside of the virtuals machines.

<img width="1404" height="1067" alt="Screenshot_20251115_190221" src="https://github.com/user-attachments/assets/ba813885-55f9-4580-8b55-b4fd1f2ae879" />


## Setting Up The Windows 10 Victim Machine

### Setting up the Windows 10 Image

Using the Windows 10 Installation Media Creation Tool, created a Windows 10 ISO file

Set up the virtual machine in Oracle VMBox, walked through the installer

<img width="968" height="937" alt="Screenshot_20251115_160318" src="https://github.com/user-attachments/assets/34e0a816-4f4b-4790-b7ef-4e48b2a54f7c" />




I then configured each of the virtual machines to be attatched to this network. I'm using virtio as the network interface, since it's more CPU effecient than the alternatives, though it does
take a little more effort to set up (Namely in setting up the drivers necessary to make it work).

### Ensuring Driver compatability
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


### Statically Assigning The Machine's IP
Next, since I'm using these machines on an isolated network within KVM and they're only going to need to talk to each other, I got started with statically setting up each of their IP addresses with IPs that belong to private non routable subnets.

For the windows machine, I configured the following under the network adapter's properties

```shell
IP: 10.10.0.10
Subnet Mask: 255.255.255.0
```

<img width="2024" height="1355" alt="Screenshot_20251117_133815" src="https://github.com/user-attachments/assets/fcc447f5-e9f3-46e9-b275-e1530428a74d" />

(Verifying to ensure that the change went through)
<img width="2024" height="1355" alt="Screenshot_20251117_134222" src="https://github.com/user-attachments/assets/82b32e98-97d2-4d14-81e3-602443c05234" />


### Setting up SPLUNK

(I renamed the VM to Windows-Machine for the sake of convenience)

I had the installer downloaded on my host, and created a disk image of it that I mounted to the windows virtual machine to install.
(I'm running the free trial of SPLUNK enterprise)


Creating the image             |  Installing it 
:-------------------------:|:-------------------------:
<img width="1440" height="329" alt="Screenshot_20251118_150017" src="https://github.com/user-attachments/assets/e074edde-fe6e-435b-84ed-52dd84205906" /> |  <img width="2081" height="1353" alt="Screenshot_20251118_150526" src="https://github.com/user-attachments/assets/5abc3f62-4d9d-4fe5-a33b-bd79b4ef0053" />


After that point, I launched Splunk in my browser within the VM (Through localhost port 8000), and configured it by adding some basic data points to be monitored and uploaded to SPLUNK from the local event logs
<img width="1995" height="1367" alt="Screenshot_20251118_154458" src="https://github.com/user-attachments/assets/564f1445-1b4f-4526-aa66-854258674e0e" />



### Setting up Sysmon

I started up by downloading Sysmon on my host machine, alongside a pretty basic configuration file. The specific configuration file I used was one that I got here on Github: https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml
I had the sysmon setup and the associated files needed for it's configuration on a USB stick, so I directed it to my windows machine in the KVM settings

<img width="1006" height="220" alt="Screenshot_20260127_215045-1" src="https://github.com/user-attachments/assets/ab459bc9-a4d1-4c3a-b27f-e81173200995" />

Once that was done with, I placed all of my sysmon related files into my Windows machine's Documents folder before opening up Powershell as an administrator and navigating to the directory. I then ran a command to set up sysmon with the configuration file from above.

<img width="1594" height="1073" alt="Screenshot_20260127_215554-1" src="https://github.com/user-attachments/assets/62e434e3-3e81-4952-b024-170903f12a32" />

Once that was through, I wanted to double-check that the installation went through. to do so, I went over to the Windows Services pane, as well as the event viewer under Windows application events. Here, I saw Sysmon was showing up under both panels, so I knew i was good to go.

<img width="1678" height="1134" alt="Screenshot_20260127_215857-1" src="https://github.com/user-attachments/assets/5d514498-4ba2-420c-94ae-887058a9836a" />



## Setting up the Kali Machine

### Setting up the Kali Linux Image


### Statically Assigning The Kali VM's IP Address
To setu up the Kali machine, I configured the following under the network properties.

```shell
IP: 10.10.0.11
Subnet Mask: 255.255.255.0
```

<img width="2011" height="1401" alt="Screenshot_20251117_134523" src="https://github.com/user-attachments/assets/0f520051-6f7b-4e3f-b8b5-c38cb511ed8d" />



