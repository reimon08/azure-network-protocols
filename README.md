<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTPS, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (22H2)
- Ubuntu Server 22.04

<h2>High-Level Steps</h2>

- Create our Virtual Machines
- Observe ICMP Traffic
- Configuring a Firewall [Network Security Group
- Observe SSH Traffic
- Observe DHCP Traffic
- Observe DNS Traffic
- Observe RDP Traffic

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

<h4>Use Remote Desktop to connect to your Windows 10 VM</h4>

<p>Since I’m on a Mac, I’m using the Microsoft Remote Desktop app. But if you’re on Windows, just open up Remote Desktop and log in from there.</p>

<p>Just grab the public IP of your Windows VM and paste it into the Remote Desktop app (or Remote Desktop on Windows) to log in</p>

<img width="1710" alt="Screenshot 2025-07-09 at 10 46 53 AM" src="https://github.com/user-attachments/assets/35c4f726-3ecd-4e1d-8b48-838c46c7fffa" />


https://github.com/user-attachments/assets/4c293c67-d711-4c25-b744-ff881a64ef2a

<h4>Once you're in the Windows 10 VM, download and install Wireshark; we’ll use it to monitor network traffic later.</h4>

<p>We’ll be using the Windows x64 installer, so make sure to grab that version when you download Wireshark.</p>

https://github.com/user-attachments/assets/509dbfe9-ff3b-47e3-ab80-2c3a2fd6f2be

<h4>Open up Wireshark on the Windows 10 VM and start a packet capture.</h4>

<p>Once you’ve opened up Wireshark, look for the Ethernet interface; it should have lines moving, which means it’s actively detecting traffic. Just highlight that interface and then click the blue shark fin icon in the top left corner to start capturing packets.</p>


https://github.com/user-attachments/assets/4c6f51b9-3e4c-4e8b-a703-6c39d876f054

<p>As you can see, all the spam flying by is just the network traffic happening in the background of this computer. It’s constantly communicating, even if you're not doing anything. Wireshark lets us watch all of that in real time.</p>


https://github.com/user-attachments/assets/2ae1b92f-b0ba-483f-8923-bd82712773c9


<h4>Observe ICMP Traffic</h4>

<p>Now we’re gonna take a closer look at ICMP. This is what gets used when you run a ping. It’s a simple way to test connectivity between devices. We'll trigger a ping in a second and watch how it shows up here in Wireshark.</p>

<p>We’re gonna ping the Linux server we created earlier, so go ahead and grab its private IP address from the Azure portal. We’ll use that to test connectivity and watch the ICMP packets show up in Wireshark.</p>

<img width="1710" alt="Screenshot 2025-07-09 at 11 45 10 AM" src="https://github.com/user-attachments/assets/a29cb6ce-8871-4d3e-ba1e-c9bb95963490" />

<p>So now from the Windows VM, go ahead and open up PowerShell and run a ping to the Linux VM’s public IP address. This will generate ICMP traffic, and you’ll see it show up live in Wireshark while the packets are flying back and forth.</p>

<p>In Wireshark, type icmp into the top filter bar and hit Enter. This will filter out everything except ICMP traffic, so you can focus just on the ping packets we’re sending from the Windows VM to the Linux VM </p>


https://github.com/user-attachments/assets/21713ea5-96d5-4db8-a2bd-5b17974f3518

<p>As you can see in PowerShell, there are four ping responses, but in Wireshark, there are eight entries. That’s because Wireshark captures both the request and the reply, so you’re seeing both sides of the conversation.</p>

<img width="1710" alt="Screenshot 2025-07-09 at 12 00 56 PM" src="https://github.com/user-attachments/assets/2dd7b3ba-cfe2-4f50-85a0-13991c61c5cb" />

<p>And if we inspect a bit deeper in the packet details, you’ll notice the MAC addresses of each VM. This is part of how devices communicate at Layer 2 of the OSI model (Data Link Layer).</p>

<img width="1710" alt="Screenshot 2025-07-09 at 12 07 23 PM" src="https://github.com/user-attachments/assets/067d440f-0300-4f8a-bb62-0c1ccae09aa6" />

<p>Now, if we go to the Internet Protocol section in the packet details, this is Layer 3 of the OSI model (Network Layer). Here, you’ll be able to see the IP addresses of both the Windows and Linux VMs</p>


<img width="1710" alt="Screenshot 2025-07-09 at 12 07 41 PM" src="https://github.com/user-attachments/assets/8a0d96f2-98d9-48ec-84f3-a41470a1f0df" />

<p>And if we expand the Data section, this is the actual payload of the packet. It’s not really important when it comes to just pinging a device, but you can see here that it’s literally just the alphabet being sent back and forth; it’s just filler data for the test.</p>

<img width="1710" alt="Screenshot 2025-07-09 at 12 22 12 PM" src="https://github.com/user-attachments/assets/39f11b42-f597-4696-b393-ca35988a0e81" />

<h3>Configuring a Firewall (Network Security Group)</h3>

<p>Now we’re gonna set up a Network Security Group (NSG), basically Azure’s version of a firewall. This lets us control what kind of traffic is allowed in or out of our VMs, so we can start locking things down and testing how it affects communication between devices.</p>

<p>First, we’re gonna initiate a nonstop/perpetual ping from the Windows 10 VM to the Ubuntu VM.
Open up PowerShell and type: ping (IP Address) -t
  
The -t flag makes the ping keep running until you manually stop it. </p>


https://github.com/user-attachments/assets/d3da5783-1d23-4311-8db9-ecfcabec4d4e


<p>What we’re basically doing here is telling the Linux VM to block all ICMP traffic coming from the Windows VM. Once we apply that rule in the NSG, any ping attempts should stop getting replies and you'll see the timeouts start showing up right away.</p>

<p>So go to your Linux VM in Azure → Networking → under Network Settings, click on Network Security Group → then go to Settings → Inbound Security Rules.
This is where we’ll block ICMP so we can see the firewall in action.</p>


<p>For the ports, we’re just gonna put *(any), ICMP doesn’t really use ports, but Azure still asks for something there.
  
Set the priority to 290 so it’s high on the list and gets processed before any allow rules. This way, our deny rule will actually take effect.</p>


https://github.com/user-attachments/assets/3672a1c8-4c7e-4dfa-a656-bc17928ff0bf

<p>As you can see, once the deny rule took effect, the ping in PowerShell started timing out.
And if you check Wireshark, you’ll notice the traffic changed, before it was request → reply, but after the rule kicked in, it’s just a bunch of requests going out from the Windows VM with no replies coming back from the Linux VM.</p>


https://github.com/user-attachments/assets/02ed02d9-1902-47fb-9107-a9056e230adb

<p>Now go ahead and re-enable ICMP traffic in the Network Security Group for your Ubuntu VM, you can either delete the deny rule or change its action to allow.

Once that’s done, jump back to the Windows 10 VM and you should see the ping start working again in PowerShell, and in Wireshark, you’ll see both requests and replies showing up just like before.</p>


https://github.com/user-attachments/assets/47b87953-1ff5-4624-8fc9-9aaa07e72dbe


<p>As you can see, once we deleted the deny rule, the requests are now getting replies again, both in PowerShell and in Wireshark. That means ICMP traffic is allowed once more and communication between the VMs is back to normal.</p>

https://github.com/user-attachments/assets/4bcdb835-f79e-4050-90a0-515f0269a062



<h3>Observe SSH Traffic</h3>

<p>So now we’re gonna observe SSH traffic between the Windows VM and the Linux VM. This will let us see what a remote login connection looks like in Wireshark and how it's different from something like a ping.</p>

<p>Back in Wireshark, go ahead and start a new packet capture.
At the top filter bar, type in: tcp.port == 22  or just type in ssh.</p>


https://github.com/user-attachments/assets/ef78e2b8-6229-4adb-ab9f-57c30428c67c

<p>Now go ahead and SSH into the Linux server that we just made. Just open up PowerShell on the Windows VM and run:

ssh labuser(private IP address)</p>

<p>Note: When you’re typing the password, it might look like nothing is being entered; that’s normal. It’s actually typing in the background; it just doesn’t show anything for security reasons. Just type it out and hit Enter.</p>


https://github.com/user-attachments/assets/baf23887-e692-4a5c-b9cc-4a7ab193655d

<p>Now, if you go back to Wireshark, you’ll see that everything happening in the SSH session is being recorded, even a single keystroke shows up as traffic. That’s because SSH encrypts and sends data with every action, so you can literally observe each interaction in real time.</p>


https://github.com/user-attachments/assets/61d40a2c-4362-4183-8b73-48ed6ca8e8b4

<p>The good thing about SSH is that everything is encrypted, so even though you can see the traffic in Wireshark, you can’t read the actual data; it's all secured. This is way more secure than something like Telnet (port 23), where everything is sent in clear text, including usernames and passwords.</p>

<img width="1710" height="1107" alt="Screenshot 2025-07-11 at 4 18 59 PM" src="https://github.com/user-attachments/assets/48b45b71-b960-473a-b254-d78198d4f6a5" />

<p>When your Windows VM connects to the Linux VM over SSH, it uses a random high-numbered port called an "ephemeral port" as the source port, like 50528. 

This is completely normal and is how the client system keeps track of outgoing connections. While the Linux VM listens on the standard SSH port 22, the client picks an available temporary port (usually between 49152 and 65535) to initiate the connection. 

This allows the system to manage multiple connections at once without confusion. Even though all connections are going to port 22 on the server, each one comes from a different source port on the client, acting like a unique return address so the server knows where to send the reply.</p>

<img width="1710" height="1107" alt="Screenshot 2025-07-11 at 4 48 58 PM" src="https://github.com/user-attachments/assets/35df7d38-68d8-4898-b5a6-4f9cd6472f49" />


<p>So now just go ahead and type exit in the PowerShell window, that’ll close the SSH session and log you out of the Linux VM.</p>

<img width="1710" height="1107" alt="Screenshot 2025-07-11 at 4 54 49 PM" src="https://github.com/user-attachments/assets/7f9704fc-de7f-400f-a37a-226895ec673c" />

<h3>Observe DHCP traffic</h3>

<p>So now we’re gonna observe DHCP traffic. Head back into Wireshark and in the top filter bar, 
  
type: dhcp or udp.port == 67
</p>

<p>So, DHCP (port 67 UDP) is a network protocol that automatically assigns IP addresses, subnet masks, default gateways, and DNS info to devices on a network.</p>

<p>Now, from your Windows 10 VM, attempt to issue your VM a new IP address from the command line</p>

<h4>So first, go ahead and open up Notepad, and type in the two commands:</h4>

<p>ipconfig/release</p>
<p>ipconfig/renew</p>

<p>Then go ahead and save the file as: dhcp.bat
And then change the “Save as type” to “All Files”
Then save it to C:\ProgramData
</p>

<p>This batch file to release the IP address and then renew it right away in one go. This lets us trigger the DHCP process without crashing or disconnecting the VM, which is perfect for observing the traffic safely in Wireshark.</p>

https://github.com/user-attachments/assets/b4ed8a40-8d4b-4887-a793-a3d78de95556

<p>Now, if everything was saved correctly, we can verify it in PowerShell.</p>

<img width="1018" height="705" alt="Screenshot 2025-07-11 at 10 48 59 PM" src="https://github.com/user-attachments/assets/74df1939-5216-43f8-8ddf-c565cb214507" />

<p>So now we're gonna run the batch file by typing this into PowerShell: .\dhcp.bat</p>

<p>Once you run it, you'll see the IP address get released and then renewed. And if you're watching Wireshark, you'll spot the DHCP Discover, Offer, Request, and Acknowledgment right away after it restarts.</p>

https://github.com/user-attachments/assets/56209f39-467a-437f-b82a-581debc14861


<p>Now if we observe Wireshark, you’ll see the whole DHCP process play out:

-After the IP was released, our Windows VM temporarily takes on the IP 0.0.0.0 and sends a broadcast to 255.255.255.255. This is the DHCP Discover phase, basically the VM is shouting, “Hey, is there a DHCP server out there?”

-Then, the DHCP server replies with another broadcast, this is the Offer phase, where it says, “Here’s an IP I can give you.”

-Next, the VM responds with a Request, saying “Yes, I want that IP.”

-Finally, the DHCP server sends an Acknowledgment, confirming the lease.

This whole back-and-forth happens in seconds, and Wireshark captures every step of it.</p>


<img width="1360" height="977" alt="Screenshot 2025-07-11 at 11 04 30 PM" src="https://github.com/user-attachments/assets/51edfe23-ee0c-43ca-93cf-d376addf7e08" />

<h3>Observe DNS Traffic</h3>

<p>Back in Wireshark, go ahead and filter for DNS traffic by typing the following into the top filter bar: dns or udp.port ==53 || tcp.port ==53</p>

<img width="1360" height="977" alt="Screenshot 2025-07-11 at 11 25 28 PM" src="https://github.com/user-attachments/assets/f4f24137-49a8-4d6d-b5b1-d8d1e35b40e8" />

<p>Now go ahead and restart the capture in Wireshark to get a clean view.

Then, open PowerShell and type: nslookup (website)</p>

<img width="1020" height="594" alt="Screenshot 2025-07-11 at 11 30 31 PM" src="https://github.com/user-attachments/assets/3bf83612-37c6-4a8e-b6c0-83801ace1e81" />

<p>And if you take a look at Wireshark, you’ll notice there’s a lot of stuff happening in the background</p>

<img width="1359" height="974" alt="Screenshot 2025-07-11 at 11 34 21 PM" src="https://github.com/user-attachments/assets/ffc2c6b5-0d2d-4e5f-bd88-f14c4ad9bf0b" />

<p>We could try copying one of the IP addresses we got from nslookup and pasting it into the web browser, but most likely it won’t work, that’s because we’re trying to access the website by its IP address instead of its domain name.

Many modern websites rely on virtual hosting, where multiple domains share the same IP. Without the proper domain in the request, the server doesn’t know which site you're asking for, so it just denies or redirects the request.</p>


https://github.com/user-attachments/assets/a041c151-d438-4f49-a72a-04486826c959

<h3>Observe RDP Traffic</h3>

<p>Basically, the Windows 10 VM we’re using is being accessed through Remote Desktop Protocol (RDP), which runs on TCP port 3389. So every time we connect to it using Remote Desktop or the Windows app on Mac, all that traffic is going over RDP and if you filter by tcp.port == 3389 in Wireshark, you’ll see all that connection activity show up.</p>

<img width="1710" height="1107" alt="Screenshot 2025-07-11 at 11 51 51 PM" src="https://github.com/user-attachments/assets/5d808802-f54e-45c6-8dfa-09045574cdf3" />

<p>So when you capture RDP traffic in Wireshark, you’ll notice a ton of what looks like packets constantly flying through. That’s because with RDP, you’re basically streaming your entire desktop session, and every little action, whether it’s moving your mouse, typing a key, or dragging a window gets sent over the network. That’s why it’s so chatty in the capture.</p>

<p>And if you compare it to SSH, it’s a lot quieter. SSH only sends data when you actually do something, like typing a command or hitting Enter, since it's just a command-line interface. But with RDP, it’s constantly streaming your entire screen session, so even just moving your mouse or hovering over something generates traffic. That’s why RDP looks way more active in Wireshark than SSH.
</p>


https://github.com/user-attachments/assets/7c9d47c6-e507-4666-80a1-c666fb1b87a2

<h3>Conclusion</h3>

<p>That wraps up this lab! We’ve gone through capturing and analyzing different types of network traffic using Wireshark and observing DNS, ICMP, SSH, to RDP. Hopefully, this gave you a better idea of what’s going on behind the scenes when devices communicate over a network. Seeing all that traffic in real time really helps build that next-level understanding.

Before you go, make sure to delete the resource group if you’re completely done with the lab. That way, you avoid getting charged for unused resources sitting around in Azure.

Thanks again for following along. I hope this project was helpful!</p>












