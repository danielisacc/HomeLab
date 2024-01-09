# Active Directory HomeLab Project
## Summary
This guide covers how to create an Active Directory (AD) within a Hyper-V VM to gain experience working with AD's and servers in general. This project is a deep dive into Active Directory and VM's and is suited for people who are rather new to AD, but have some experience working with computers and as such, this document is rather verbose and descriptive. In order to make this experience more pleasant for everyone, I included a table of contents with links to allow anyone viewing to search for information that is valuable to them. This project covers the content regarding...
- [Enabling Hyper-V Manager on <b>Windows 10 Pro</b>](#Preparing-Your-VM)
- [Creating and Configuring a Windows 2019 Server Virtual Machine (VM)](#Creating-Your-VM)
- [Deploying and Configuring an Active Directory (AD)](#Configuring-an-Active-Direcotory)
- [Deploying and Configuring RAS/NAT onto the AD server](#Deploying-and-Configuring-RAS)
- [Deploying and Configuring DHCP onto the AD Server](#Deploying-and-Configuring-DHCP)
- [Creating, troubleshooting, and running PowerShell script for User Creation](#PowerShell-Scripting)
- [Deploying and connecting a <b>Windows 10 Education</b> VM to the VM Network](#Endpoint-Device-VM-Deployment)
### What is Active Directory
Active Directory(AD), often called Active Directory Domain Services(AD DS) can be a rather broad topic due to the flexibility of the services it provides. At it's core an AD stores data objects. This is means it doesn't just store files like a file system does, instead storing "Objects" within it's structure. These objects include Users, Computers, Groups and Group Policies. In other words an AD is where information and rules regaurding Users and the Devices within a Network are stored, managed, and enforced. This is a simplified explanation of AD since the "Domain Services" of AD DC includes much more than storing data as the Server that runs AD often times has more responsibilies outside of storing objects. This includes RAS/NAT which is a service that allows users within the AD's internal Network to connect to the greater internet, while the DHCP service allows the AD to act as a network "Gateway" similar to a router, assigning IP addresses to users within the network. With both of these services deployed on an AD the users within the internal network are never connected to the external internet, instead using the AD as a barrier. This is how Organizations use AD and is why I set mine up this way as well.
## Preparing Your VM
[What is a VM](#What-is-a-VM) | [Why HyperV](#Why-HyperV) | [Enable HyperV Manager](#Enable-HyperV-Manager) | [Installing Windows ISOs](#Installing-Windows-ISOs) | [Windows Liscenses](#Windows-Liscenses)
### What is a VM
A Virtual Machine (VM) is an enviornment that uses its "Host" computers resouces to create a virtual computer(s) that exists within the host. This allows developers to effectively run multiple computers with different operating systems from within one computer. This has many uses to developers such as testing malware without placing their own network or devices at risk of infection or in our case configuring and running multiple devices without having to purchase the hardware to run those devices. We use a Hypervisor platform, which is a software that allows us to create, configure and interact with VM's. In our case, our Hypervisor platform is Hyper-V.

### Why HyperV
While researching how to create and run an AD VM I was unable to find any videos that walk through the process of using Hyper-V. This is likely due to the fact that Hyper-V requires the Pro version of whatever Windows OS is running, meaning it is less accessible, meanwhile Oracles VirtualBox is free to use and most users can run it. However, I already have Windows 10 Pro, so I can use it and it is significantly better for windows when compared to VirtualBox. Since Hyper-V is native to Windows, it requests access to resources directly from the motherboard rather than VMware or VirtualBox, which are external software that request resource management from the OS currently running on the workstation. This means that Hyper-V has a smoother experience with lower CPU and memory use than other VM's.

### Enable HyperV Manager
As long as you have a Windows version that is Pro, it is rather easy to enable the Hyper-V Manager.
1. The first step is to select the Windows Start menu and type "Turn Windows features On/Off" and open the accordingly named program.
2. The Features window opens where you will check the "Hyper-V" and "Windows Hypervisor Platform" checkboxes as seen in the Image below.
3. Click okay to save the changes, wait for the installation to finish, then restart the computer to enable the features.<br>
![](/images/image46.png)
### Installing Windows ISOs
Now that Hyper-V is enabled and ready to deploy your next VM, the next step is to download the Operating System(OS) ISO file, which is a bootable file that the BIOS runs when it detects the file. This is overly simplified, but in terms of VM's it is all that you need to know. Installing ISO's can seem daunting to people who rarely download OS's, let alone ISO files as most people keep the Windows version their PC's come with and simply upgrade when the next one comes out. Although maybe confusing, it is quite fast and easy to download ISO's as long as you know what to look for and where to look. I provided a link to follow below, but if you would rather search for an ISO yourself there are some rules to follow.
- Never download an ISO from a provider that is not the OS Producer themselves. In terms of this project that means from Microsoft websites only. ISO's are bootable Images and although this project is in a VM if you used it on an actual PC, an ISO from an untrusted site may be dangerous.
- Before downloading anything from the OS Producer, ensure that the website is HTTPS:// as this ensures that you are using a trusted and secured website as many times threat actors will use URL's similar to trusted websites and make it look like the trusted webpage.
With that in mind, the first step is to visit the website [Windows 2019 Server ISO](https://info.microsoft.com/ww-landing-windows-server-2019.html). This link will take you to a Microsoft webpage that will provide you with a free trial for the Windows Server Liscese for 180 days. If you attend college or High School, you may be able to get the liscense for free using an Azure Student Account, but you would need to ask an advisor or search for resources on your schools webpage to find this information. To attain the Free Trial and the ISO, enter your information and click download, noting the location and file name to access later.

The next Download is the Windows 10 Pro exe file from [Windows 10 EXE file](https://www.microsoft.com/en-us/software-download/windows10) or the Windows 10 Education ISO from an Azure Student Account. if you use Windows 10 Pro, you will need a Liscensing key which I will explain how to get later on.
1. Unlike the Windows Server ISO, Windows 10 uses a download wizard, which is an exe file. Download this file and follow the download wizard.
2. Accept the terms and conditions
3. Select Installation Media when it asks "What do you want to do?" then click next
4. Select next on the Architecture screen
5. Select ISO file on the "Choose which media to use" screen and click next
6. Save the file in a location you can find and name the file something easy to find such as "Windows10ISO.ISO"
### Windows Liscenses
There are two types of OS's on the internet which are Open-Source and Proprietary. Open-Source media is free to use, download, and anyone can edit it, while proprietary is pay to use. Linux Distrubutions are the most well known Open-Source media, which is why it is seen as the most customizable, the most affordable(because it's free), most times the safest, and the most used OS in the world. Proprietary OS's such as Windows and MacOS are the most well known proprietary OS's and as such are not free to use. MacOS is based on an Open-Source kernel, but can be difficult to get on a VM. Windows is the most used by end-users and by far the most popular OS in the world, especially in First world countries, mostly since it is easy to use and comes pre-installed on most Ready To Ship (RTS) PC's, meaning it's what most people have used their entire lives and is much easier to use than Linux for everyday use.

Windows however, is not free to use like Linux and due to this, using Windows OS's require liscense keys to use, even for VM's. Liscenses require a purchase to get and Microsoft sells them for around $100 for the Pro version of Windows 10 and charges a yearly fee for Server liscenses. This large price tag can be reduced or outright skipped a couple of ways.
1. The free way was mentioned before, which is using your college or high school, which often times provides either an Azure account for their students for free, or offer Windows Education to students through their IT team. This is not the fastest or easiest method to get Windows liscense keys, but it is by far the cheapest(Again FREE).
2. The next cheapest method is purchasing a liscense from a Windows Liscense seller. This is legal, but also can be dangerous if you aren't careful which sites you visit. You can get Windows 10 Pro liscenses as low as $6 using this method. To stay safe, you should ensure that any card transactions take place over an HTTPS:// website. This means not just checking that the home page is https, but the actual webpage the card information is entered into. Although unsafe in some regaurds, this is one of the easiest methods to get a liscense and by far cheaper than purchasing it from Microsoft directly.
3. If you already have a Windows liscence for an old PC, you can use that if is not in use. If the old liscense is Windows 7, you can easily use it for this project, but some of the steps may not be the same. If you have an old liscense, but it is not Pro, you can purchase an upgrade from microsoft, which is much cheaper than purchasing the key outright, but is still more expensive than buying it from a third party.

Whatever method is used, it is essential you get a liscense key in order to continue with this project.

## Creating your VM
[VM Wizard](#VM-Wizard) | [Virtual Switch Manager](#Virtual-Switch-Manager) | [Deploying and Installing Windows Server](#Deploying-and-Installing-Windows-Server) | [Setting Up Static IPv4](#Setting-Up-Static-IPv4)

### VM Wizard
#### Hyper-V Manager
To open the Hyper-V Manager, press the Windows Key and type "Hyper-V" and select "Hyper-V Manager". Once the Manager is open there are 5 panes visible.
- The pane on the left is the **Console-Tree** and will not be used in this project, since I only have one host device.
- There are three panes in the middle
  - The top pane is the **Virtual Machines** pane and this is where you can access your VM's
  - The middle pane is the **Checkpoints** pane and this is where you can access previous VM states if a critical failure of the VM occures, this is how you can go back to a previous state when you know it worked.
  - the bottom pane is the **Details** pane which gives basic details regaurding the VM such as it's creation date and other notes
- The right pane is the **Actions** pane and this is where you can create or import VM's, access the Virtual Switch Manager and preform other actions on the Hypervisor platform, not specifically to any particular VM.
- There is an additional pane that appears when you select a VM that takes the name of the VM selected. This pane allows you to control the VM such as starting, connecting and stopping the VM.
#### Creating your VM
Creating a VM in Hyper-V is very easy. To start select *New* from the **Actions** pane and select *Virtual Machine...*, where the **Virtual Machine Creation Wizard** opens.
1. Before You Begin - Click Next
2. Specify Name and Location - I entered *DC1* in the Name field, but you can enter whatever you want. Just make sure to name it something you recognize. Click Next
    1. I did not specify a location for the VM to be saved in, but if you can change the location if you would like. Click Next
3. Specific Generation - I highly recommend Generation 2 if you have a 64 bit PC, as it is newer, provides better features and an overall user experience. Click Next
4. Assign Memory - I used 2048MB of memory, but you can use anything more that that. Using more will provide a smoother experience, but for most operations, it is unnecessary. Click Next
5. Configure Network - Hyper-V automatically creates an External Network called *Default Switch*, select this, but we will create another switch later. Click Next
6. Connect Virtual Hard Disk - I only changed the size field under *Create a Virtual Hard Disk* to 50GB. This is dynamically used, meaning not all 50GB will be used by the VM, only up to. Click Next
7. Installation Options - Select the radial option *Install an operating system from a bootable CD/DVD-ROM*, then select the radial option *Image File(ISO)* and click *Browse..*. From there select the Windows 2019 Server ISO you downloaded and click Open. Click Next
8. Summary - Review the summary screen and ensure it is simialar to the image below, then click Finish to Create the VM

![](/images/image37.png)
### Virtual Switch Manager
#### What is a Virtual Switch
A virtual switch emulates a network adapter within a virtual enviornment. These adapters are used to create a network between the greater internet, the host, and other VM's on the host. There are three different types of virtual switches...
- External - these switches allow the VM to connect to the internet through the host's NAT card.
- Internal - These switches allow the VM to only connect to the host PC and other VM's on the host through a network, similar to a corperates internal network.
- Private - These switches are similar to the Internal switch, except the host is unable to connect to the network, instead only VM's within the host can connect.
#### Create a Virtual Switch
The Virtual Switch Manager is a user interface that allows you to create, delete and configure virtual switches that can then be connected to VM's through the VM's hardware settings. For the AD we are creating, the VM needs to have an internal switch so users can connect to the AD and an external switch to connect those users to the internet. In the VM Creation Wizard we already added the default external switch, but now we need to create and attack the internal switch to the VM. The manager is opened through the **Actions** pane by selecting the *Virtual Switch Manager...* button. This opens the manager.
1. The manager automatically opens to the **Create Virtual Switch** pane where you can create a virtual Switch. Select *Internal* and click *Create Virtual Switch*
2. This opens the **Virtual Switch Properties** pane, where you can configure the switch. I changed the name to Internal for simplification, but you can name it anything you can recognize later. Click ok to create the switch.
3. The switch should appear on the left in the **Virtual Switches** pane. You can close the manager.
#### Installing the Switch
Now that the Switch has been created, we can install it into the DC1 VM and later onto the CLIENT1 VM. This process is rather easy and begins with right clicking the VM name displayed in the **Virtual Machines** pane and selecting *settings*.
1. It automatically opens to the **Add Hardware** pane. Within this pane select *Network Adapter* and click *Add*.
2. This opens the **Network Adapter** pane. Within this pane select *Internal* or whatever you named your adapter from the **Virtual Switch** dropdown options. Click Apply
3. The Internal adapter should appear in the **Hardware** pane under **Network Adapter**. Click Okay to close the settings window.

### Deploying and Installing Windows Server
Now that the VM is fully set up, it is ready to be started. To start the VM, select the VM from within the **Virtual Machines** pane. The **"DC1"** pane should open under the **Actions** pane where you can select *Start*, which will start the VM, then select *Connect* to open the VM in a seperate window. The VM will open requesting you to press any button. This is the only time you should press anything when prompted on this screen as this begins the Windows installation. Press any button.
1. Languages - The **Windows Setup** window appears. Click Next
2. Install - Select *Install Now* and wait for it to install.
3. Select the OS you want to Install - After the install completes, you are prompted to select which os to install. Select *Windows Server 2019 Standard Evaluation (Desktop Experience)*. This edition comes with the standard features I need and uses the Desktop Experience which is the Graphical User Interface (GUI). Click Next
4. Terms and Conditions - Accept the Terms and Conditions. Click Next
5. Install Options - Select *Custom Install* since there isn't an existing OS to upgrade.
6. Installation Drive - Click Next, since there is only one drive installed on the VM.
7. Windows begins the final installation. **DON'T** click anything until the installation finishes, especially if prompted upon the VM restarting.
8. Customize Settings - Create a secure password you can remember, eg.(8 char, 1 Uppercase, 1 Number, 1 Symbol...) and click Finish. The VM is now running with Windows Server 2019!
### Setting Up Static IPv4
#### Static IP
Now that the server is installed and running, we need to configure the IP address of the Internal Network. The reason why is that an IP address is dynamic by default, meaning that it can change for several reasons, but since we want all computers on the network to be able to access the internal network, the IP address can't change. This is called assigning a static IP address. The first step is opening the network settings by clicking on the connections button at the bottom right corner in the task bar, then selecting the network that you are connected to. It should just be called *Network*.
1. In the **Settings** the **Ethernet** pane automatically opens. from within this pane select the *Change adapter settings* link. This will open the **Network Connections** window.
2. From this window there should be two connections with ambiguously named **Ethernet 1 & 2** connections. We need to rename the connections for later and for simplicity. To establish which adapter is the internal and external is rather easy.
    - Since the external adapter is actively connected to the internet, it automatically is assigned as the **Network**. This means that under the name **Ethernet**, one adapter is assigned to **Network** and another is assigned **Unidentified Network**.
3. Rename the adapter with **Network** under it's name "External" by right clicking it and selecting rename. Rename the other one "Internal".
4. Now that you know which adapter is the internal one, right click **Internal** and select *Properties*.
5. The **Internal Prioperties** window appears, where you will select *Internet Protocol Version 4 (TCP/IPv4)* and select the *Properties* button.
6. This opens the **IPv4 Properties** window where we will configure the static address. First select *Use the Following Address* and enter the fields as listed below...
    1. IP Address: 172.16.0.1
    2. Subnet Mask: 255.255.255.0
    3. Default Gateway: (Empty)
7. Under **Use the following DNS server address**
    1. Preffered DNS Server - 127.0.0.1
8. What we just did was assign the Server the Internal IPv4 Address 172.16.0.1, which we will use a lot throughout this project, so make sure to reference regularly. We then left the Default Gateway empty, since this server will become the defualt gateway once everything is set up, meaning the IP address 172.16.0.1 will become the Default Gateway. We lastly set the DNS server to ourselves, meaning that we will direct traffic to the appropriate servers ourselves. This is important as although this puts extra work on the server, it also allows us to secure the network much better and help prevent DNS Spoofing.
9. Click Okay to close and save the changes.
#### Changing Server Name
This step is not necessary, but it does clarify everything just a little bit and makes things appear nicer and clear. Changing the name is easy and fast. To start, right click the windows start button and select *System* from the menu. 
1. This opens the **Settings** window with the **About** pane opened by default. Scroll down until a button labeled *Rename this PC* appears and click it.
2. In the window that pops up, type something memorable. I changed the name to DC1. Click Next. The system will need to restart.
3. After the reboot, if you have not done so already, I recommend saving a checkpoint and naming it "Server Established".

## Configuring an Active Directory
[The Server Manager](#The-Server-Manager) | [Deploying AD](#Deploying-AD) | [Post Deployment](#Post-Deployment) | [Adding an Admin](#Adding-an-Admin)
### The Server Manager
Upon logging into the server, the Server Manager automatically opens. All of the tools and features of all roles and services that are deployed onto the server are accessibly through the manager. This means I will be refering to the manager often, so it's best you get aquainted with it.
- On the left pane of the manager is the **Directory** pane where you can access the **Dashboard** among any of the roles and server groups that we establish through this process.
- In the middle are two panes
    - The top pane is the **Welcome to server manager** pane, where you will quickly be able to add new roles and features in the **Configure this local server** quick select frame.
    - The bottom pane is the **Roles and Server Groups** This is similar to the **Directory** pane, except it gives additional information and more quick access options. It provides role and server states as well using green and red coloring. If the color of the banner of a role is green, it means that the server is running well, but if the color is red, then the server is recieving warnings or errors on this role or group. It is normal for the server groups during this project to display red on **1 Services**
- The task bar at the top of the screen is where all tools and resources are found for managing the server and it's roles. The **Flag** is where additional steps for setups will appear such as Post Deployment steps for the AD. The **Manage** tab is where you can manage groups and group policies among other things. The **Tools** tab will be used regularly throughout this process and contains all the tools and wizards to complete tasks.

The names of these panes and buttons will be references regualry throughout this project, so make sure to revisit if you can't find a button I reference on the server manager.
### Deploying AD


### Post Deployment


### Adding an Admin


## Deploying and Configuring RAS
[]() | []() | []() | []() | 

## Deploying and Configuring DHCP
[]() | []() | []() | []() | 

## PowerShell Scripting


## Endpoint Device VM Deployment
