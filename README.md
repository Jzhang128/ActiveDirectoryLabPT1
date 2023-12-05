<h1> Active Directory Lab Part 1: Configure Active Directory Environment</h1>

<h2>Description</h2>
We will create a lab environment utilizing Windows Server 2019 along with Window 10 VM to emulate a client workstation. Windows Server 2019 will be used as our domain controller, which will allow for authentication and authorization for users that we establish within AD. The overall goal is to get acquainted with Active Directory, understanding what it provides for us and how it works.

<h2>Prerequisite </h2>

- <b>Download Oracle Virtualbox to host virtual machines. You can download Virtualbox here. Ensure you download the correct platform that is compatible with your computer operating system and install the extension pack.  </b>
- <b>Download Server 2019 here and  Windows 10 OS here and specify the file type as ISO for both. I recommend placing these files on your desktop for easier access for future steps. </b> 

<h2>Tutorial:</h2>

<p align="center">
<h3> Configuring Domain Controller in Virtualbox </h2>
1. Open Virtualbox and create the server 2019 Virtual machine first by pressing “new”. 
Input name as “DC” and select “other windows(64-bit)” for version. 
 <br/>
<img src="https://i.imgur.com/spB5MJC.png" height="80%" width="80%" alt="AD1-1"/>
<br />
<br />
2. Press continue and assign at least 2048(2GB) of ram and 2 CPU to the VM. Depending on your system, you can allocate more RAM or CPUs. I do not recommend going below 2 gigs of RAM.  <br/>
<br />
<br />
3. Continue through the default virtual disk setup and assign 50GB. <br/>
<br />
<br />
4. Once the virtual machine is created, go to the settings and navigate to the network tab. The first adapter that is attached to NAT should be there by default. We will add a second adapter by clicking the “Adapter 2” tab and check the box to enable network adapter. In the “attached to:” field, select “internal network”. Press ok.  <br/>
<img src="https://i.imgur.com/4CcV1go.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
5. Double click the VM we labeled as DC and select Server 2019 ISO to install. Press start.   <br/>
<br />
<br />
6. After you boot into the installation, you will get to a screen that asks you which operating system you want to install. Choose “Windows Server 2022 Standard Evaluation (Desktop Experience).” This option allows us to have the GUI option instead of a command prompt interface if you were to select an option without the desktop experience.  <br/>
<img src="https://i.imgur.com/6Yc67rT.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
7. Once download is complete, create a password for the built-in administrator account.  <br/>
<br />
<br />
8. Rename the machine by typing “rename” in the search bar and name it as “DC”. <br/>
<br />
<br />
9. Restart the Virtual machine.  <br/>
<br />
<br />
 
<h3> Configuring Virtual Network</h3>
1. Our network will be set up with 2 nics, one dedicated to the internet and another for the internal network. The network for the internet will automatically get an address from your home router. The internal network will be set up manually.
<br />
<br />
2. First, click the network icon located on the bottom right of the DC screen. Select “change adapter options” and you should notice 2 connection types.
<br />
<br />
3. Right click on each of the connections and select “status”, then “details”.
<br />
<br /> 
4. View the IPv4 details and rename the connection with the IPv4 address that starts with 169.254.x.x as “Internal” and rename the other as “Internet”. 
  <img src="https://i.imgur.com/TAcvkMD.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
5. We will manually assign an IPv4 address to the connection labeled as “Internal” by right clicking that connection and selecting “properties”.
<br />
<br />
6. Click “Internet Protocol Version 4 Properties” and choose the option to “use the following IP address”. Input these values to it’s specified field:</br>
  IP address: 172.16.0.1</br>
  Subnet Mask: 255.255.255.0</br>
  Gateway: Leave it empty(our domain controller will serve as default gateway)</br>
  DNS: 127.0.0.1(the computer will use itself as a DNS server)</br>
<img src="https://i.imgur.com/9OpvXNs.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
7. Install RAS/NAT to allow other Windows 10 clients to be on the private network and still be able to access the internet through the domain controller. To do this, type “server manager” on the search bar. Click “add roles and feature” and click next.
<br />
<br />
8. Add the role “Remote Access” and choose the “routing” option. Click “next” through each screen and install. 
<img src="https://i.imgur.com/lCpmMpF.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
9. After it finishes installing, click “tools” at the top right of the server manager and select “Routing and Remote Access”. 
<img src="https://i.imgur.com/wklj36V.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
10. Click the domain controller(DC) and right click to select “Configure and Enable Routing and Remote Access”.
<br />
<br />
11. Click next and choose the “NAT” option. On the next screen, ensure the connection label “Internet” and “Internal” is visible under “Use the public interface to connect to the internet”. If the box is empty, cancel and repeat this step.
<img src="https://i.imgur.com/LtsLzOU.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
12. Select the connection labeled as “Internet” and click finish. 
<br />
<br />

<h3> Setting DHCP Feature to Domain Controller </h3>
1. Click “Add roles and Feature” on the server manager.
<br />
<br />
2. Add the role “DHCP Server”. This feature allows our internal client to get an IP address to browse the internet even though they would be on a private network.
<br />
<br />
3. Select tools on top right of the server manager, and click DHCP to setup DHCP scope.
<br />
<br />
4. Right click the IPv4 option and select “New Scope”. Click next and name it as “172.16.0.100-200”. 
<br />
<br />
5. Specify the start IP address as 172.16.0.1 and end IP address as 172.16.0.200. Specify the subnet mask as 255.255.255.0 and click next.
<img src="https://i.imgur.com/lxY97eN.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
6. Click the "next" button on the next three screens. 
<br />
<br />
7. On the Router(default gateway) screen, specify the domain controller as the IP address(172.16.0.1). Click the add button and next.
<img src="https://i.imgur.com/sXI4lRf.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
8. Continue to click "next" and finish setup. 

<h3> Adding Active Directory Feature to Domain Controller</h3>
1. Type “server manager” on the search bar. Click “add roles and feature” and click “next” through each screen. Then add the feature “Active Directory Domain Services” and install it. 
<img src="https://i.imgur.com/2ajVaMq.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
2. After it has been installed, you will notice a yellow flag on the top right of the server manager. Click the flag with the yellow exclamation point triangle, and click “promote this server to a domain controller.” This will allow us to create the domain.
<img src="https://i.imgur.com/GJmzx4C.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
3. Click the “Add a new forest” button and specify a root domain name. I will name mine as “prototype.com” but you can name yours whatever you like.
<img src="https://i.imgur.com/DlnGnQp.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
4. Click next and on the Domain Controller Options page, specify a DSRM password. Leave everything as is and click through each screen to install.
<br />
<br />
5. Create a standard user under the User organization unit. This would mimic a typical person within a company with their login credentials.
<br />
<br />
6. We will create a dedicated admin account instead of using the built-in administrator account. To do this, press start(window key) and dropdown the “window administrator tools” to select  “active directory users and computers”.
<br />
<br />
7. Create a new Organization Unit(OU) to put the admin account in by right clicking the domain name(protoype.com in my example). Hover to “new” and select “organizational unit.”
<br />
<br />
8. Name the OU as “Admins” and create a new user as adm_(first initial and last name). 
<br />
<br />
9. Note that the account just created isn’t an admin yet. To make it an admin, right click the account, go to properties and select the “member of” tab.
<br />
<br />
10. Add “domain admins” role and apply.
<br />
<br />
11. Sign out and login as the admin account. 
<br />
<br />

<h3>Joining Window 10 Client to Active Directory Domain</h3>
1. Create a new virtual machine in Virtualbox and name it as “Client 1” with Windows 10(64-bit) version selected. 
<br />
<br />
2. Assign ram and storage as needed.
<br />
<br />
3. Once the VM is created, go to network setting and select “Internal Network” for 
Adapter 1. This will allow the Client 1 VM to receive a DHCP address from the domain controller we configured earlier. 
<br />
<br />
4. Double click the Client 1 VM and browse to where the Windows 10 ISO file is stored. 
<br />
<br />
5. Go through the installation process and choose Windows 10 Pro version to be able to join the host to a domain. The VM will restart after WIndow 10 pro is installed.
<br />
<br />
6. Go through the out of box window setup and create a local account. 
<br />
<br />
7. Login to the account and open the command prompt using the search bar. Type the “ipconfig” command to ensure you are assigned a private IP address from the DHCP server and ping google.com to test DNS.
<img src="https://i.imgur.com/EAFBOBr.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
8. To join the host to the domain, go to settings and choose the system option. 
<br />
<br />
9. Scroll toward the bottom of the system setting page and click
“Rename this PC (advanced)”.
<br />
<br />
10. Click the “Change..” button to rename the host to “Client 1” and join the domain by typing the name of the domain that was setup earlier. 
<img src="https://i.imgur.com/xaQUzI1.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
11. Click the “Ok” button. You will be prompted with a Window Security popup where you need to enter the Domain Admin account we created earlier in active directory.
<br />
<br />
12. Go ahead and restart the pc when prompted.
<br />
<br />
13. Login with a standard user account you created earlier. 
<br />
<br />
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
