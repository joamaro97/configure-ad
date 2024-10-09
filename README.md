<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Installing Active Directory in Azure</h1>
This lab demonstrates the steps I took to install Active Directory using Azure. I will be using this as the foundation for labs I will do in the future. I will be using two VMs on Azure that are on the same vnet. This particular lab will focus on just one of the VMs, which will be used to install Active Directory and configured to be the domain controller. The other VM will be used as a client that will join later in a future lab. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 Pro (21H2)

<h2>Installation Steps</h2>

<p>
<img src="https://i.imgur.com/fBRzEZ3.png" height="80%" width="80%" alt="Installation Steps"/>
</p>
<p>
Before using the VMs, it is important to set the domain controller VM's IP address as static. By default, the VMs will not be able to communicate with each other if both have dynamic IPs despite being on the same vnet. If we do not make the necessary change, the client will not be able to join the domain that will be created later. On the Azure portal, click on the Networking tab on the domain controller VM. Click on the Network Interface and open the IP configurations tab. Toggle the Assignment switch to be Static and save your changes. We are making sure the domain controller has a static IP and it will be used as a reference when we make configurations.
</p>
<br />

<p>
<img src="https://i.imgur.com/hE1XNtg.png" height="80%" width="80%" alt="Installation Steps"/>
<img src="https://i.imgur.com/hlDtnbY.png" height="80%" width="80%" alt="Installation Steps"/>
<img src="https://i.imgur.com/IbIJWiC.png" height="80%" width="80%" alt="Installation Steps"/>
</p>
<p>
After setting the static IP, it is time to log in to the client VM and see if there is connectivity to the domain controller. Using ping -t (domain controller private ip address), will show that the connection is being timed out. On the domain controller VM, we need to enable ICMPv4 on the local Windows Firewall. Within the search bar, type wf.msc to open Windows Defender Firewall. Click on Inbound Rules and enable the Core Networking Diagnostics - ICMP Echo Request rules. Returning to the client VM will show that the ping is now resolving without errors.
</p>
<br />

<p>
<img src="https://i.imgur.com/ktSoNmA.png" height="80%" width="80%" alt="Installation Steps"/>
</p>
<p>
It is now time to install Active Directory on the domain controller VM. With Server Manager open, click on Add Roles and Features and click Next. Confirm the private IP address of the domain controller VM. In the Server Roles tab, click on Active Directory Domain Services. Click Add Features, click Next, then Install. Next we have to promote the server into a domain controller. In Server Manager, there is a warning sign in the top right corner under a flag. Click on that flag and click Promote this server to a domain controller. Click on Add a new forest and specify a domain name. In my case, I will use ernestotest.com. Specify a password for the domain and click on Next on each screen and Install.
</p>
<br />

<h2>An Important Note </h2>

When logging back in to the domain controller VM through Remote Desktop Connection, it is important to log in with the context of the domain. Type out the domain path and then the name of the user. For example: mydomain.com\labuser. In my case, it is ernestotest.com\labuser. Now that Active Directory is installed, configurations can be implemented in future labs and the client VM will be able to join the domain that was created.
