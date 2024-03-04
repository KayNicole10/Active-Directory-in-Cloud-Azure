# Active-Directory-in-Cloud-Azure

<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 

<h2>Deployment and Configuration Steps</h2>

- Step 1: Create Resource Group
- Step 2: Deploy Virtual Machines
- Step 3: Connect to Virtual Machines
- Step 4: Change the Windows Server 2022's IP address to static
- Step 5: Install Active Directory Domain Services role on the Windows Server 2022 virtual machine
- Step 6: Promote the server to a domain controller
- Step 7: Configure a new Active Directory forest and domain
- Step 8: Set up DNS role and configure DNS settings
- Step 9: Join the Windows 10 virtual machine to the Active Directory domain
  
<h2>Create Resource Group</h2>

<p>
<img src="https://i.imgur.com/OrKQUd3.png" height="70%" width="70%" alt="Resource Group"/>
  <img src="https://i.imgur.com/uUZoIPA.png" height="70%" width="70%" alt="Add Resource Group"/>
</p>
<p>
To begin setting up your Active Directory environment in Azure, start by creating a resource group in the Azure portal. This resource group will serve as a container for all the components of your virtual machines. When creating the resource group, choose a distinctive and memorable name that reflects its purpose. All associated resources, such as networking configurations, storage, and security settings, are encapsulated within the designated resource group. </p>
<br />

<h2>Deploy Virtual Machines</h2>
<p>
<img src="https://i.imgur.com/wHoY9cV.png" height="70%" width="70%" alt="Virtual Machines"/>
</p>
<p>
Proceed to deploy two virtual machines within Azure: one running Windows Server 2022 (DC-1) and the other running Windows 10 (Client-1). Ensure that both virtual machines are deployed within the same virtual network, which helps facilitate communication and connectivity between them.</p>
<br />

<h2>Connect to Virtual Machines</h2>
<p>
<img src="https://i.imgur.com/nc1QyIT.png" height="70%" width="70%" alt="RDP"/>
</p>
<p>
The next step is to connect to the two virtual machines using Remote Desktop Connection (RDP). In order to do this, follow these steps: 
<ol>
  <li>Note down or copy the public IP addresses assigned to each virtual machine.</li>
  <li>Confirm that the network security group (NSG) associated with the virtual machine allows inbound Remote Desktop Protocol (RDP) traffic on port 3389.</li>
  <li>On your local computer, open the Remote Desktop Connection application. You can usually find it by searching for "Remote Desktop Connection" in the Start menu (Windows) or Spotlight (macOS).</li>
  <li>Enter the public IP address of the DC-1 virtual machine in the "Computer" field.</li>
  <li>Click "Connect" to initiate the connection.</li>
  <li>If prompted, enter the username and password for the virtual machine. This will typically be the credentials you set up during the virtual machine deployment process.</li>
  <li>Repeat the above steps to connect to the Client-1 virtual machine using its public IP address.</li>
</ol>
</p>
<p>The first time you connect to each virtual machine, you may be prompted to accept a certificate. This is normal and ensures the connection is secure. Click "Yes" or "Connect" to proceed.</p>
<p><img src="https://i.imgur.com/HLXn516.png" height="70%" width="70%" alt="VM"/>
</p>
<p>Once the connection is established, you will have remote access to the desktop of each virtual machine.</p>
<br />

<h2>Change the Windows Server 2022's IP address to static</h2>
<p>
<img src="https://i.imgur.com/TM9k81j.png" height="70%" width="70%" alt="static"/>
</p>

<p>
Setting the IP address of the Windows Server 2022 (DC-1) to static is crucial for several reasons. A static IP address ensures consistent and reliable communication between the Azure virtual machine and other network resources, such as other virtual machines. Without a static IP address, the IP address assigned to the virtual machine could change, leading to potential connectivity issues. In environments where DNS is utilized for name resolution, a static IP address ensures that the DNS records associated with the Windows Server 2022 virtual machine remain accurate and consistent. This is particularly important for domain-joined machines where DNS records are used for authentication, name resolution, and service location.</p>

<p>
Follow these steps:

<ol>
  <li>Navigate to the virtual machines section in the Azure portal.</li>
  <li>Select the Windows Server 2022 virtual machine named "DC-1".</li>
  <li>In the virtual machine settings, locate and select the network interface associated with the virtual machine.</li>
  <li>Within the network interface settings, locate the IP configuration section.</li>
  <li>Change the IP assignment method from "Dynamic" to "Static".</li>
  <li>Save the changes.</li>
</ol>
</p>

<h2>Optional: Connection Test</h2>

<p>
To ensure connectivity to both virtual machines, we can log into Windows 10 (Client-1), and try to ping Windows Server 2022 (DC-1) using it's private IP address. 
</p>

<p>
<img src="https://i.imgur.com/giVmAtP.png" height="70%" width="70%" alt="Ping"/>
</p>

<p>
As we can see, the request timed out because DC-1's firewall is blocking ICMP traffic. To solve this, we can log into DC-1 and open the firewall. Select "Inbound Rules", select "protocal", search for "Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In)" under the ICMPv4 protocal. There should be two so enable both. 
</p>

<P>
<img src="https://i.imgur.com/TvpWrr7.png" height="70%" width="70%" alt="Enable"/>
<img src="https://i.imgur.com/Smvprz8.png" height="70%" width="70%" alt="Firewall"/>
</P>

<p>
Going back to Client-1, we can see that the ping is now successful. We can now be sure that there is connectivity between the twk virtual machines and we can now move forward. </p>

<p>
<img src="https://i.imgur.com/mYimD3X.png" height="70%" width="70%" alt="Susccessful"/>
</p>

<h2>Install Active Directory Domain Services role on the Windows Server 2022 virtual machine</h2>
