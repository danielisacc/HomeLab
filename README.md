# Active Directory HomeLab Project
## Summary
This guide covers how to create an Active Directory (AD) within a Hyper-V VM to gain experience working with AD's and servers in general. This project is a deep dive into Active Directory and VM's and is suited for people who are rather new to AD, but have some experience working with computers and as such, this document is rather verbose and descriptive. In order to make this experience more pleasant for everyone, I included a table of contents with links to allow anyone viewing to search for information that is valuable to them. This project covers the content regarding...
- [Enabling Hyper-V Manager on <b>Windows 10 Pro</b>](#Preparing-Your-VM)
- [Creating and Configuring a Windows 2019 Server Virtual Machine (VM)]()
- [Deploying and Configuring an Active Directory (AD)]()
- [Deploying and Configuring RAS/NAT onto the AD server]()
- [Deploying and Configuring DHCP onto the AD Server]()
- [Creating, troubleshooting, and running PowerShell script for User Creation]()
- [Deploying and connecting a <b>Windows 10 Education</b> VM to the VM Network]()
### What is Active Directory
Active Directory(AD), often called Active Directory Domain Services(AD DS) can be a rather broad topic due to the flexibility of the services it provides. At it's core an AD stores data objects. This is means it doesn't just store files like a file system does, instead storing "Objects" within it's structure. These objects include Users, Computers, Groups and Group Policies. In other words an AD is where information and rules regaurding Users and the Devices within a Network are stored, managed, and enforced. This is a simplified explanation of AD since the "Domain Services" of AD DC includes much more than storing data as the Server that runs AD often times has more responsibilies outside of storing objects. This includes RAS/NAT which is a service that allows users within the AD's internal Network to connect to the greater internet, while the DHCP service allows the AD to act as a network "Gateway" similar to a router, assigning IP addresses to users within the network. With both of these services deployed on an AD the users within the internal network are never connected to the external internet, instead using the AD as a barrier. This is how Organizations use AD and is why I set mine up this way as well.
## Preparing Your VM
[Why HyperV](#Why-HyperV) | [Enable HyperV Manager](#Enable-HyperV-Manager) | [Installing Windows ISOs](#Installing-Windows-ISOs) | [Windows Liscenses](#Windows-Liscenses)
### Why HyperV
While researching how to create and run an AD VM I was unable to find any videos that walk through the process of using Hyper-V. This is likely due to the fact that Hyper-V requires the Pro version of whatever Windows OS is running, meaning it is less accessible, meanwhile Oracles VirtualBox is free to use and most users can run it. However, I already have Windows 10 Pro, so I can use it and it is significantly better for windows when compared to VirtualBox. Since Hyper-V is native to Windows, it requests access to resources directly from the motherboard rather than VMware or VirtualBox, which are external software that request resource management from the OS currently running on the workstation. This means that Hyper-V has a smoother experience with lower CPU and memory use than other VM's.

