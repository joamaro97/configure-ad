<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Installing and Configuring Active Directory in Azure</h1>
This lab demonstrates the steps I took to install and configure Active Directory using Azure. This will be used as the foundation for future labs. I will use two VMs on Azure that are on the same vnet. This particular lab will focus on just one of the VMs, which will be used to install Active Directory and configure it as the domain controller. The other VM will be used as a "Client" to join later in a future lab. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell 

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 Pro (21H2)

<h2>Installation Steps</h2>

<p>
<img src= "https://i.imgur.com/MKc14nH.png" height="80%" width="80%" alt="Installation Steps"/>

<img src= "https://i.imgur.com/XMRiKLQ.png" height="80%" width="80%" alt="Installation Steps"/>
</p>
<p>
Before using the VMs, we must set the IP address as "static" in the domain controller. By default, the VMs cannot communicate with each other if both have dynamic IPs despite being on the same vnet. If we do not make the necessary changes, the client will not be able to join the domain that will be created later. On the Azure portal, click on the "Networking" tab on the domain controller VM. Click on the Network Interface and open the IP Configurations tab. Toggle the Assignment switch to Static and save your changes. We ensure the domain controller has a static IP and will be used as a reference when we make configurations.
</p>
<br />

<p>
<img src="https://i.imgur.com/bQSgkVL.png" height="80%" width="80%" alt="Installation Steps"/>
<img src="https://i.imgur.com/Bntmkc0.png" height="80%" width="80%" alt="Installation Steps"/>
<img src="https://i.imgur.com/xDvc7F5.png" height="80%" width="80%" alt="Installation Steps"/>
</p>
<p>
After setting the static IP, it is time to log in to the client VM and see if there is connectivity to the domain controller. Using the command ping (domain controller private IP address) will show that the connection is being timed out. On the domain controller VM, we need to enable ICMPv4 on the local Windows Firewall. Within the search bar, type wf.msc to open Windows Defender Firewall. Click on Inbound Rules and enable the Core Networking Diagnostics - ICMP Echo Request rules. Returning to the client VM will show that the ping is resolved without errors.
</p>
<br />

<p>
<img src="https://i.imgur.com/I84gX76.png" height="80%" width="80%" alt="Installation Steps"/>
<img src="https://i.imgur.com/34GoC1C.png" height="80%" width="80%" alt="Installation Steps"/>
<img src="https://i.imgur.com/8XRm4Z7.png" height="80%" width="80%" alt="Installation Steps"/>
<img src="https://i.imgur.com/hkjY3Ec.png" height="80%" width="80%" alt="Installation Steps"/>
</p>
<p>
  
Now is the time to install Active Directory on the domain controller VM. With Server Manager open, click on Add Roles and Features and click Next. Confirm the private IP address of the domain controller VM. In the Server Roles tab, click on Active Directory Domain Services. Click Add Features, click Next, then Install. Next, we have to promote the server to a domain controller. In Server Manager, there is a warning sign in the top right corner under a flag. Click on that flag and click Promote this server to a domain controller. Click on Add a new forest and specify a domain name. In my case, I will use ernestotest.com. Specify a domain password, click Next on each screen, and Install.
</p>
<br />

<h2>An Important Note </h2>

When logging back into the domain controller VM through Remote Desktop Connection, logging in with the domain context is important. Type out the domain path and then the user's name. For example, my domain is adtest.com. If I want to log in as client it would be like adtest.com/labuser. Now that Active Directory is installed, configurations can be implemented in future labs, and the client VM can join the created Domain.





<h1>Active Directory Configuration Steps </h1>
Now, with Active Directory installed, we can proceed with configuring it. For this part, I will allow access to a client (which would be through the other VM) while also creating user accounts to mimic a usual active directory ecosystem <br />


</p>
<p>
<img src="https://i.imgur.com/PTodxj5.png" height="80%" width="80%" alt="Configuration Steps"/>
<img src="https://i.imgur.com/v0OMzPI.png" height="80%" width="80%" alt="Configuration Steps"/>
<img src="https://i.imgur.com/qvsH7qx.png" height="80%" width="80%" alt="Configuration Steps"/>
</p>
<p>
Now that Active Directory is installed on the domain controller VM, it is time to create new Organizational Units and Users. With the Active Directory Users and Computers console open, right-click on the domain you created (in my case, labtest.com) and create a new Organizational Unit (OU). I have created two Organizational Units, _EMPLOYEES and _ADMINS. I named them like this because the Powershell script will be used later. Within the _ADMINS OU, I created a new User called John Doe. John's account will be given administrative privileges through the use of a Security Group. To grant admin privileges to a User, right-click on the user and open their Properties. Click Member Of, then Add to apply to the appropriate security group. 
  
In this case, I added John to the Domain Admins security group. From now on, I will be using John's account to make any further changes. I will log off as a lab user and log in as john_admin.
</p>
<br />

<p>
<img src="https://i.imgur.com/3kiT1QB.png" height="80%" width="80%" alt="Configuration Steps"/>
</p>
<p>
Before the client can join the domain, it is important to configure the DNS settings first. The DNS server has to point to the domain controller's private IP address. On the Azure portal, open the Networking tab and click on Network Interface. In the DNS servers, enter the domain controller's private IP address and save the changes. Restart the client VM in order to ensure the DNS changes are saved.

</p>
<br />

<p>
<img src="https://i.imgur.com/C7rCAkB.png" height="80%" width="80%" alt="Configuration Steps"/>
<img src="https://i.imgur.com/anZRthh.png" height="80%" width="80%" alt="Configuration Steps"/>
<img src="https://i.imgur.com/V6F8olA.png" height="80%" width="80%" alt="Configuration Steps"/>
</p>
<p>
It is now time to make the client VM join the domain. In the System menu of the client VM, click on Rename this PC (advanced) and Change. Enter the domain and necessary credentials in order to let the client join the domain. I am logging in as John Doe for the purposes of the lab. It is important to note that the login credentials have to be input within the context of the domain path. The client should now be part of the domain. On the domain controller, the client should now appear in Computers in the Active Directory Users and Computers panel.
</p>
<br />

<p>
Before users in the domain can use the client computer, Remote Desktop has to be enabled for non-administrative users. While logged in as the administrator (in my case, Jane), open System Properties. Click on Remote Desktop and Select users that can remotely access this PC. Allow Domain Users access to Remote Desktop. Non-administrative users can now log in to Client-1. Normally a Group Policy can do the same and allows changes to many systems at once. For the purposes of this lab, a Group Policy won't be used to make this change.
</p>
<br />

<p>
<img src="https://i.imgur.com/oEUh06j.png" height="80%" width="80%" alt="Configuration Steps"/>
<img src="https://i.imgur.com/nYG6fxu.png" height="80%" width="80%" alt="Configuration Steps"/>
</p>
<p>
Creating users can be done manually or through the use of a script. For this lab, I will be using a PowerShell script. The PowerShell script can be found <a href="https://github.com/AsiaPonder001/BunchofUsers/blob/main/README.md?plain=1)"> here. </a> On the domain controller, open PowerShell ISE as an administrator (and make sure you are logged in with an admin account on the domain controller). Create a new file and paste the script into ISE console. Run the script and observe the accounts being created. 
</p>
<br />

<p>
<img src="https://i.imgur.com/TfNfRK1.png" height="80%" width="80%" alt="Configuration Steps"/>
</p>
<p>
After creating the users, Client-1 can now be signed in as one of the new users that were created from the PowerShell script. Pick a name and simply sign in to the client with the context of the domain. In my case, it is ernestotest.com\bon.rovej.
</p>
<br />

<h2>Lessons Learned</h2>

Doing this lab has made me learn how to set up Active Directory and join clients to the domain. I also created users and assigned the necessary permissions. Active Directory is not difficult to learn despite all the menu navigation that takes place. This lab is a segway for me to learn about DNS settings in-depth and file permissions in action. I will go into detail about these topics in other labs.
