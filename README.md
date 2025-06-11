<p align="center">
  <img src="https://i.imgur.com/pU5A58S.png" height="40%" width="70%" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Deploying On-Premises Active Directory in Azure</h1>
This walkthrough demonstrates how to set up a traditional Active Directory environment using Azure-based virtual machines.<br />

<h2>Platforms and Technologies Used</h2>
<ul>
<li>Microsoft Azure (VM Infrastructure)</li>
<li>Remote Desktop Protocol (RDP)</li>
<li>Active Directory Domain Services (AD DS)</li>
<li>PowerShell</li>
</ul>

<h2>Operating Systems Utilized</h2>

<li>Windows Server 2022</li>
<li>Windows 10</li>

<h2>Overview of Key Setup Steps</h2>
<ul>
<li>Provision Azure Resources</li>
<li>Confirm Network Link Between Machines</li>
<li>Install and Configure Active Directory</li>
<li>Add Administrator and Standard Users</li>
<li>Join Client-1 to the Domain (myadproject.com)</li>
<li>Enable RDP Access for Non-Admin Users</li>
<li>Create Test Users and Verify Access</li>
</ul>
<h2>Step-by-Step Configuration</h2>

<br />
<h3 align="center">Provision Resources in Azure</h3>
<p>
  Begin by creating a virtual machine for your Domain Controller (DC-1) using Windows Server 2022:
</p>
<p>
  <img src="https://i.imgur.com/gaAzjvb.png" height="75%" width="100%" alt="Resource Group"/>
  <img src="https://i.imgur.com/hubTfey.png" height="75%" width="100%" alt="VM Windows Server"/>
</p>
<p>
  Then create a client machine named "Client-1" running Windows 10. Make sure both VMs share the same Resource Group and Virtual Network:
</p>
<p>
  <img src="https://i.imgur.com/XyEmv8f.png" height="75%" width="100%" alt="VM Windows"/>
</p>
<p>
  Assign a static private IP to DC-1’s network interface card:
</p>
<p>
  <img src="https://i.imgur.com/KHU9kC4.png" height="75%" width="100%" alt="Static IP"/>
</p>
<p>
  Use Network Watcher to confirm that both machines are on the same VNet:
</p>
<p>
  <img src="https://i.imgur.com/rFpHLdQ.png" height="75%" width="100%" alt="Topology"/>
</p>

<br />
<h3 align="center">Verify Network Communication</h3>
<p>
  Connect to Client-1 via Remote Desktop and run a continuous ping to DC-1’s private IP using `ping -t`:
</p>
<p>
  <img src="https://i.imgur.com/bnPM9tX.png" height="75%" width="100%" alt="Perpetual Ping"/>
</p>
<p>
  On DC-1, allow inbound ICMPv4 echo requests by modifying the local firewall settings:
</p>
<p>
  <img src="https://i.imgur.com/ZpPyEkt.png" height="75%" width="100%" alt="Enable ICMPv4"/>
</p>
<p>
  Back on Client-1, verify successful responses:
</p>
<p>
  <img src="https://i.imgur.com/8o3OfjY.png" height="75%" width="100%" alt="Ping Success"/>
</p>

<br />
<h3 align="center">Install and Configure Active Directory</h3>
<p>
  Sign in to DC-1 and install the Active Directory Domain Services role:
</p>
<p>
  <img src="https://i.imgur.com/A1V9XJ5.png" height="75%" width="100%" alt="Install AD"/>
</p>
<p>
  Promote the server to Domain Controller status:
</p>
<p>
  <img src="https://i.imgur.com/zi15fw4.png" height="75%" width="100%" alt="Promote DC"/>
</p>
<p>
  Create a new forest (e.g., myactivedirectory.com — later changed to myadproject.com):
</p>
<p>
  <img src="https://i.imgur.com/DCFUVrM.png" height="75%" width="100%" alt="New Forest"/>
</p>
<p>
  Reboot and log in to DC-1 as: `myadproject.com\labuser`
</p>
<p>
  <img src="https://i.imgur.com/7UakWMQ.png" height="75%" width="100%" alt="FQDN Login"/>
</p>

<br />
<h3 align="center">Add User Accounts in Active Directory</h3>
<p>
  In the Active Directory Users and Computers (ADUC) console, create Organizational Units (OUs) named "_EMPLOYEES" and "_ADMINS":
</p>
<p>
  <img src="https://i.imgur.com/cYmv0r7.png" height="75%" width="100%" alt="OU Creation"/>
  <img src="https://i.imgur.com/v02CBPI.png" height="75%" width="100%" alt="OU Creation"/>
</p>
<p>
  Add a user named Jane Doe with the username `jane_admin`:
</p>
<p>
  <img src="https://i.imgur.com/h546E6L.png" height="75%" width="100%" alt="User Creation"/>
</p>
<p>
  Add her to the "Domain Admins" Security group:
</p>
<p>
  <img src="https://i.imgur.com/mnLwTgq.png" height="75%" width="100%" alt="Domain Admins"/>
</p>
<p>
  Disconnect from DC-1 and reconnect as `myadproject.com\jane_admin`. This will now be your primary admin login.
</p>
<p>
  <img src="https://i.imgur.com/xWZ4Kol.png" height="75%" width="100%" alt="Login as Admin"/>
</p>

<br />
<h3 align="center">Connect Client-1 to the Domain</h3>
<p>
  Set the DNS configuration of Client-1 to use DC-1’s private IP address:
</p>
<p>
  <img src="https://i.imgur.com/1KRsjI6.png" height="75%" width="100%" alt="DNS Settings"/>
</p>
<p>
  Restart Client-1 through the Azure Portal.
</p>
<p>
  Log in using the local admin credentials and join it to the domain:
</p>
<p>
  <img src="https://i.imgur.com/50wszcP.png" height="75%" width="100%" alt="Join Domain"/>
</p>
<p>
  Confirm the device Client-1 appears in ADUC under the "Computers" container, then move it to a new OU named "_CLIENTS":
</p>
<p>
  <img src="https://i.imgur.com/vB1n9m0.png" height="75%" width="100%" alt="Move Client"/>
</p>

<br />
<h3 align="center">Enable RDP for Standard Users on Client-1</h3>
<p>
  Log into Client-1 using the `jane_admin` account.
</p>
<p>
  Access system settings and enable Remote Desktop.
</p>
<p>
  Grant “Domain Users” permission to access the machine via RDP.
</p>
<p>
  Standard users can now log into the machine.
</p>
<p>
  Normally, this would be handled through Group Policy to manage multiple machines simultaneously.
</p>
<p>
  <img src="https://i.imgur.com/8BfpT3s.png" height="75%" width="100%" alt="RDP Access"/>
</p>

<br />
<h3 align="center">Bulk Create Users and Test Login</h3>
<p>
  Log into DC-1 as `jane_admin`.
</p>
<p>
  Open PowerShell ISE with administrative privileges.
</p> 
<p>
  Paste in the script from this GitHub repository: https://github.com/MiltonCWhite/Configure-ad/blob/main/adscript.ps1
</p>
<p>
  <img src="https://i.imgur.com/0i8uApf.png" height="75%" width="100%" alt="PowerShell Script"/>
</p>
<p>
  Run the script and observe new accounts being generated.
</p>
<p>
  <img src="https://i.imgur.com/6QOGzs6.png" height="75%" width="100%" alt="User Creation"/>
</p>
<p>
  Open ADUC and view the new user entries. Try logging into Client-1 using one of the new users. Be sure to reference the password from the script.
</p>
<p>
  <img src="https://i.imgur.com/ZZCfiCp.png" height="75%" width="100%" alt="Employee Accounts"/>
  <img src="https://i.imgur.com/7gBpNzN.png" height="75%" width="100%" alt="Choose User"/>
  <img src="https://i.imgur.com/cqsddjn.png" height="75%" width="100%" alt="Login Test"/>
</p>

<br />
<h3 align="center">Final Thoughts</h3>
<p>
  Hopefully this guide gave you a clearer look at how Active Directory is structured and how VMs can securely communicate within a virtual environment. This setup is accessible from both PCs and Macs (Mac users need the Microsoft Remote Desktop app).
</p>
<p>
  Before wrapping up, remember to remove all Azure resources you deployed to avoid unnecessary costs.
</p>
<p>
  Sign out of all RDP sessions, delete the resource group(s) used in this lab, and double-check to ensure they’re fully removed.
</p>
