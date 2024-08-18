
<p align="center">
<img src="https://imgur.com/cnzAGld.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
Outline of implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2> What is Active Directory (AD)? </h2>
Active Directory (AD) is a Microsoft service for managing users, computers, and resources in a network. It centralizes security and administrative tasks, provides authentication and authorization, and organizes data into domains and organizational units. AD ensures data consistency and easy resource management across the network.
<br>
<br/>

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

  <h2>Network Map</h2>

<img src="https://imgur.com/dTZ1Zi0.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<h2>High-Level Deployment and Configuration Overview</h2>

- Step 1: Setup Resources in Azure
- Step 2: Ensure Connectivity between the client and Domain Controller
- Step 3: Install Active Directory
- Step 4: Create an Admin and Normal User Account in AD
- Step 5: Join Client-1 to the domain (mydomain.com)
- Step 6: Setup Remote Desktop for non-administrative users on Client-1
- Step 7: Create 10,000 users with Powershell ISE and attempt to log into client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>


<img src="https://imgur.com/hpEBJg4.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/2SwEy26.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

Setup Resources in Azure
- Create the Domain Controller VM (Windows Server 2022) named “DC-1”
- Take note of the Resource Group and Virtual Network (Vnet) that got created
- Set Domain Controller’s NIC Private IP address to be static
- Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet 
- Ensure that both VMs are in the same Vnet (the topology can be checked with Network Watcher)

<img src="https://imgur.com/Nd3ztB0.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/Le7Mj66.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/ZcrtnNB.png" width="80%" alt="Disk Sanitization Steps"/>


Ensure Connectivity between the client and Domain Controller
- Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping)
- Login to the Domain Controller and enable ICMPv4 in on the local windows Firewall
- Check back at Client-1 to see the ping succeed

<img src="https://imgur.com/mx5ruqH.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/5BEl89X.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/VsoqDPf.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

Install Active Directory
- Login to DC-1 and install Active Directory Domain Services
- Promote as a DC: Setup a new forest as mydomain.com (can be anything, just take note of it)
- Restart and then log back into DC-1 as user: mydomain.com\labuser

<img src="https://imgur.com/lJ63ahF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/urx5JXF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/T11Cb9p.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

Create an Admin and Normal User Account in AD
- In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”
- Create a new OU named “_ADMINS”
- Create a new employee named “Jane Doe” (same password) with the username of “jane_admin”
- Add jane_admin to the “Domain Admins” Security Group
- Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”
- User jane_admin as the admin account from now on

<img src="https://imgur.com/QtnZVzw.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/CqxJVix.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

Join Client-1 to the domain (mydomain.com)
- From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address
- From the Azure Portal, restart Client-1
- Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart)
- Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain
- Create a new OU named “_CLIENTS” and drag Client-1 into there (Step is not necessary, but is just for organizational purposes.)

<img src="https://imgur.com/qsS4DVf.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

Setup Remote Desktop for non-administrative users on Client-1
- Log into Client-1 as mydomain.com\jane_admin and open system properties
- Click “Remote Desktop”
- Allow “domain users” access to remote desktop
- Client-1 can now be logged into as a normal, non-administrative user 
- Normally this would be done with Group Policy allowing the change of MANY systems at once

<img src="https://imgur.com/LUrD7gv.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/2LeyV5P.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

Create a additional users and attempt to log into client-1 with one of the users
- Login to DC-1 as jane_admin
- Open PowerShell_ise as an administrator
- Create a new File and paste the contents of the script into it
  - (https://github.com/cornerstonian/configure-ad/blob/main/generate-names-create-users.ps1))
- Run the script and observe the accounts being created
- When finished, open ADUC and observe the accounts in the appropriate OU
- attempt to log into Client-1 with one of the accounts (take note of the password in the script -- "Password1")

<img src="https://imgur.com/5NaX2OU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<h2 align="center"> <br>Active Directory is now deployed & 10,0000 users have been created with a PowerShell 🗄️  </h2>








