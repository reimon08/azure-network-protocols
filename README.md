<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Step 1
- Step 2
- Step 3
- Step 4

<h2>Actions and Observations</h2>

<h4>Create a Resource Group</h4>

<p>First, we’re gonna create a new Resource Group in Azure. This is where we’ll keep everything for this project organized in one place. Give it a clear name like "ad-file-share-lab" and make sure to pick the same region you'll be using for the rest of your resources, keeping everything in one region avoids issues down the line.</p>


https://github.com/user-attachments/assets/96ff6b25-c65a-4ec2-a8f6-2cf476efbbf2

<h4>Create a Windows 10 Virtual Machine (VM)</h4>

<p>Now we’re gonna create a Windows 10 VM. During the setup, make sure to select the Resource Group you created earlier. When it asks about networking, go ahead and let it create a new Virtual Network (VNet) and Subnet, and rename the VNet to Lab2-Vnet to keep things clean and organized.

For the image, pick Windows 10 Pro 22H2, and for the size, anything with 2 vCPUs and 8 GiB of memory will be good enough for this project.

Set the username to labuser and the password to Cyberlab123!.</p>


https://github.com/user-attachments/assets/5c385e0a-4a6f-467d-a0cf-7189af9501e0

<h4>Create a Linux (Ubuntu) VM</h4>

<p>Next, we’re gonna create a Linux VM running Ubuntu.
During setup, make sure to select the same Resource Group and use the exact same Virtual Network (Lab2-Vnet) we created earlier; this is important so both VMs can talk to each other later on.

For the image, select Ubuntu Server 22.04 LTS, and for the size, pick anything with 2 vCPUs and 8 GiB of memory. For authentication, choose Username and Password instead of SSH to keep things simple for this project.</p>

<p>username:labuser password:Cyberlab123!</p>


https://github.com/user-attachments/assets/600c94ec-428e-4a6c-ac92-5be08760dd29

<h4>Ensure Both VMs Are in the Same Virtual Network</h4>

<p>Double-check that both the Windows 10 VM and the Ubuntu VM are connected to the same Virtual Network (Lab2-vnet) and Subnet. This is important because they need to be on the same network so they can communicate with each other during the file sharing portion of the lab.</p>


https://github.com/user-attachments/assets/19951607-b86e-4e11-a895-c03bf2502648




