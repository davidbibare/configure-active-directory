<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Deploying Active Directory in Azure</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure virtual machines. We will also be creating a Domain user along with additional users in this tutorial.<br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>Deployment and Configuration Steps</h2>

- Create a virtual network and virtual machines in Microsoft Azure
- Setting up a domain controller & a client desktop virtual machine
- Configure DNS settings to connect both desktops for remote access
- Ping to the DNS server with Powershell to confirm connection
- Install Active Directory on virtual machine
- Create a Domain User for Active Directory
- Add additional users with a script in Powershell

<h2>Azure Virtual Machine Steps</h2>

<p>
<img src="https://github.com/user-attachments/assets/bf50a935-1aeb-45cd-95c0-407279e1b9be" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
To begin this lab, create your "Resource Group" in Microsoft Azure and name it "Active-Directory-Lab".

Remember the region you've picked for the resource group, this will have to stay the same for your virtual network and virtual machine for the following steps.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/3798b29c-2e36-47f2-9deb-649486f109a1" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go back to home in Azure and create your virtual network.

Select the Resource Group you just created and name this virtual network "Active-Directory-VNet", then hit review and create. Now on to making your virtual machine.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/1865d7c9-e832-4826-a7b2-5ddb6fbbd9a7" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now let's make your virtual machine. Choose your Resource Group you just created & then name your virtual machine "DC-1".
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/3e5fa800-65af-4516-a2cf-b9aae4e0bc80" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
For your image select "Windows Server 2022 Datacenter x64 Gen2" this is going to be your operating system for the virtual machine.

For the size I selected "Standard_E2_v3 - 2 vcpus, 16GB memory" you can pick any size that has at least 2 vcpus & 8GB or more of memory, this will ensure your virtual machine can handle the workload in this lab.

As seen above, make your username and password for the login to this virtual machine. I used "labuser" and "Password123!" as my password. You can make up your own, just make sure you dont forget!

<img src="https://github.com/user-attachments/assets/a18c8b50-2257-405d-a64e-d8cf35e80a6f" height="50%" width="50%" alt="Disk Sanitization Steps"/>

Make sure you check the box below for your licensing before you continue.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/69b7ef8c-2da7-4613-bc19-bbc2175163f5" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go to the "Networking" part of this virtual machine and make sure you have selected the virtual machine you created, now hit "Review+Create"
</p>
<br />
Now let's create our client's virtual machine
<p>
<img src="https://github.com/user-attachments/assets/61acb944-e6d8-4796-ad33-398864f6f743" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Repeat the same process as you did with the first virtual machine and name this virtual machine "Client-1". Select the image "Window 10 Pro, version 22H2-x64 Gen2" for the operating system.

Now enter your username and password, You can use the same one as the first virtual machine. Afterwards select the Licensing box & select your virtual network and press "Create"
</p>
<br />
Before moving on, we need to go back to our virtual machine "DC-1" and set its private IP address as static. Azure automatically creates a dynamic IP Address, we need to change it to static so there's no possibility it will change.

This ensures we can Remote Desktop Connect with "Client-1" VM using the IP Address of "DC-1" with no issues.
<p>
<img src="https://github.com/user-attachments/assets/3db0fcc2-969f-4b99-9d66-9f9d471d7ffb" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go to the "Networking" section in "DC-1", drop down to "Network Settings" and click on the "Network Interface Card (NIC)". 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/69c75203-4b62-4e42-a961-98d33f4b601b" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go to the the "IP Configurations Settings" and click on "ipconfig1".
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/674101fe-aedc-44e8-8760-9193f7dba8f0" height="50%" width="50%" alt="Disk Sanitization Steps"/>
</p>
<p>
Under "Allocation" change "dynamic" to "static". Keeping your IP Address "10.0.0.4" the same. You might have a different IP Address assigned, just keep that one the same. Now hit "Save".
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/e76b2936-2daf-497c-8098-06b7e8b148c5" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go to "DC-1" to copy its public IP address, we are now going to load up our desktop's "Remote Desktop Connection" and paste it to connect to the virtual machine.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/738afe62-47c9-4a3d-8ca6-352e294efe0b" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Enter your login credentials to connect to the virtual machine.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/9da1cac9-253c-4b86-864f-075e3f2015b0" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once your Domain Controller loads in, you're going to right click the Windows start button, click "Run" and type "wf.msc" for Windows Firewall.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/8ec3046f-7a7b-42b1-97af-a6a35ad0f205" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click on "Windows Defender Firewall Properties" to turn off Firewall status on all 3 "Domain, Private, & Public Profile" settings, hit "Apply" then "Okay". The firewall is now off.
</p>
<br />
We now need to set "Client-1" DNS settings to "DC-1" private IP address. So go back into Azure and Copy "DC-1" Address "10.0.0.4".

</p>
<p>
<img src="https://github.com/user-attachments/assets/bcffa1f6-129d-490d-9221-ed7d6320f2f6" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go into "Client-1" under "Networking" click "Network Settings" and go into the "NIC Settings" to configure.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/44fd55d3-dd88-4569-a9f7-4c9d9cc0aa93" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Under "DNS Servers" change from "Inherit from virtual network" to "Custom", then paste "DC-1" DNS IP address and save.
</p>
<br />
Go back to the virtual machine tab in Azure and by "client-1" click restart so changes are applied!

Once its finished restarting copy "Client-1" public IP address like before and login into the VM using your desktop's "Remote Desktop Connections".
<p>
</p>
<br />

<h2>Pinging your Desktops</h2>

Now we are going to ping DC-1's private IP address to ensure there's an established connection to both virtual machines.
<p>
</p>

Open up the "Client-1" virtual machine. Search up "Powershell" in the Windows search command bar and open it as an Administrator.
<p>
<img src="https://github.com/user-attachments/assets/017f65e7-5a19-4531-aecf-76fa691bd8d4" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the command prompt, type "ping 10.0.0.4". This will ping to "DC-1" ensuring there is a connection to the virtual machine. Sending 4 packets and receiving 4 back as a test. 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/547eb654-7e00-4eb2-908a-a08081c14be4" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next type in "ipconfig /all" into powershell, now we can see that the DNS Server is in fact connected to DC-1's DNS Server "10.0.0.4"
</p>
<br />


<h2>Installing Active Directory</h2>


Now that both of our virtual desktops are ready & connected. We are finally able to install Active Directory.
<p>
</p>
Login to "DC-1", click on Windows Start button and pull up "Server Manager".

<img src="https://github.com/user-attachments/assets/f1730762-6800-4d40-a802-05bb34a187cf" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
You are now on the Server Manager Dashboard, Go to "Add roles and features". Continue to click next until you get to "Server Roles". Click on the box to install "Active Directory Domain Services", "Add features" and continue to "Next" to install.

Now that Active Directory is installed, we now need to configure it to be an actual Domain Controller. 
</p>
<br />

</p>
<p>
Go back to the Server manager and in the upper right corner, click on the flag with the yellow triangle beside it and click on "Promote this server to a Domain" under "Post-deployment configuration". 
</p>
<br />

<img src="https://github.com/user-attachments/assets/99e4b415-c1bc-41b6-9981-d34a2647ac88" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  
Select "Add a new Forest", name the "Root Domain Name" as "mydomain.com". Click next and enter a password "EX: Password1". Next uncheck "Create DNS delegation" Then Install!
</p>
<P>
<img src="https://github.com/user-attachments/assets/c15452a6-d413-4fbd-858c-d6dd47ce8255" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now it will automatically restart, once it's done log back in on Remote Desktop Connection to "DC-1". We need to specify that we are logging in as a Domain Controller User.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/5b226c03-07cb-4129-9c58-2123ca14e8d1" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Update your credentials to "mydomain.com\labuser" with the same password "Password123!"

</p>
<br />


<h2>Creating a Domain Admin User</h2>

<p>
Now lets create an Admin and normal User.
<p>
</p>


<p>
<img src="https://github.com/user-attachments/assets/eb26f7a5-9645-4d20-af7c-58bea028604c" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go to your DC-1 virtual machine & click Windows start and "Windows Administrative Tools", pull up "Active Directory Users and Computers"
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/e11b56b0-aba4-44a2-8084-8a64cc38ab07" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Right click on "mydomain.com", click "New" and create an "Organization Unit".
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/56d27776-027e-4ce4-b75e-8dbe0769fda5" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We are going to name this "_EMPLOYEES". This is where we will then later put our employees in as Users.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/b601d0ee-2f73-4353-a0f7-18cd54287fa8" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now let's create another one but go to "Users" & name this one "_ADMINS" (spelling here is vital!)
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/b4e89ed3-8a9f-4cf4-9d31-a63bfe7f6a68" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We are now going to create our Admin user. I chose "Jim John" for this Admin but you can use whatever you prefer. Make the user login name "jim_admin".
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/7fada27e-18d4-4c9b-bc1a-b9f1afa363fe" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
After that go to the Properties of "Jim John" & under "Member Of", "Add" Jim as a "Domain Admin", click "Check names" then hit "OK".

Now that you have created your first Admin User. Let's log out of this DC-1 VM and log back in using your new admin user login!
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/a76a8885-b262-4031-aee2-cd5158efbe6e" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log back in with "mydomain.com\jim_admin" and your password.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/65eec4e5-e687-4d09-9779-9321e6e4a808" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once logged in, right click the windows start button and go to "System". Then on "Rename this PC" click "Change" to change this domains name to "mydomain.com" 
<p>

<p>
<img src="https://github.com/user-attachments/assets/b980509f-0c9e-4872-8507-301cfc3dcab1" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
</p>
You can now go back to "Active Directory and Computer" under "Computers" you will see that "Client-1" is now associated to the "mydomain.com" server.
<p>
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/76a7cca7-19d8-47d0-9b51-676b88e9dcab" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create another "Organization unit" and name it "_CLIENTS", drag and drop "Client-1" computer into the new "_CLIENTS" folder. 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/6e646e07-f61d-4cdf-8808-2e051ae4ed90" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log into "Client-1" with your domain user login credentials as "mydomain.com\jim_admin".
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/66031a89-614a-42ef-b667-5cfdfcee9dd8" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Under Windows System, go to the "Remote Desktop" section to allow the domain users desktop remote access. Click "Select users that can remotely access this PC". 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/b187c43f-f809-4cd0-b5a2-06f7720ccf1b" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click "Add" and "Check names" as "Domain Users" then hit "OK". You now have Client-1 enabled for Remote Access.

Now let's go to "DC-1" and load in a bunch of users with a script.
</p>
<br />


<h2>Creating Additional Users with Powershell</h2>


<p>
Log back into "DC-1" as "Jim_admin"
<p>
<img src="https://github.com/user-attachments/assets/9d6c721e-d78b-4571-8c44-530cdaddc2b7" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the search bar, look up "Windows Powershell IISE" as an Administrator.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/6219cb07-10f7-4493-8e53-1bb1cfca5f9e" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now you have opened up Powershell on your virtual machine.
</p>
<br />


<p>
<img src="https://github.com/user-attachments/assets/0fa187a1-fd1b-43fb-a361-c78e7dafe90b" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Copy this script made by Josh Madakor on his Github, "https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1". This script generates 1,000 users in your "_EMPLOYEE" folder.
<p>
<br />


<p>
<img src="https://github.com/user-attachments/assets/3fb40c2d-5a05-4fe5-9c6d-2cd9e80fec29" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Copy and paste this to a folder on your PC and Name it "create-users" then open this file up in your Powershell.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/dc4bce5b-ad9e-4f77-a31e-aa246780a31c" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click the "Play" button at the top to run this script. This may take a while but if you don't need 1,000 users, you can stop the script at anytime.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/35ae3d37-c1b1-4566-aac8-c7b3672d14f8" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once complete, go back into your Active directory & computers and look at all the new users in the "_EMPLOYEES" folder. Choose one to then log into "Client-1" with, I chose "cob.tic" but you will have a different combination of users so pick any that you like.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/a74b5eda-31b3-449a-bf7f-a2dd06179990" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log back into "Client-1" as your new user you have made with "mydomain.com\cob.tic" and "Password1" as your login.
<br />


<h2>End of the Lab</h2>

<p>
</p>


Congratulations! You've completed this lab and made your first Admin and Users for your Domain.
<p>
Have an amazing day and have fun with toying around in Active Directory on Microsoft Azure. There are so many possibilities and different things to learn!

<p>
</p>
<br />


