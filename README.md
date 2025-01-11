<p align="center">
<img src="https://i.imgur.com/aMMGyHQ.jpeg" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />

<h1>Preparing Active Directory Infrastructure in Azure</h1>

 ### [YouTube Demonstration](https://youtube.com)

<h2>Description</h2>
In this project I create two VMs (Virtual Machines), one running Windows Server, to act as a Domain Controller. The other VM will act as a client, running Windows 10 that will join the domain. In later projects I will deploy AD, run a script that will create users in the domain, which I can log into from the client VM, then manage the accounts and update the group policies, all to simulate a real life IT environment!  <br/>
<br />


<h2>Environments and Utilities Used</h2>

- <b>Microsoft Azure</b>
- <b>Virtual Machines</b>
- <b>Remote Desktop Connection</b>


<h2>Operating Systems Used </h2>

- <b>Windows Server </b>
- <b>Windows 10</b>

<h2>Project Walk-through:</h2>

Navigate to Microsoft Azure and create a resource group:

![image](https://github.com/user-attachments/assets/d9d59002-b94a-46c2-925d-aea5b3818918)

Next, create a virtual network like so:

![image](https://github.com/user-attachments/assets/1a07c9f2-0f31-471b-8cb4-728c2d6111d3)

Once my resource group and network is created, I'll create and set up the virtual machine that will act as our Domain Controller. For the image, make sure you use Windows Server:

![image](https://github.com/user-attachments/assets/15476e1e-6c27-490a-939d-9c1a9f2873d7)

![image](https://github.com/user-attachments/assets/2547b9ab-3077-4d1e-ad87-3ef5d8a2ef3b)

In the Networking tab of this VM, I'll make sure it will create itself on the virtual network I just created. I'll leave all other settings default and create this VM:

![image](https://github.com/user-attachments/assets/76d04da8-71a3-4041-88ee-d0ebd34ffee2)

Now, I'll create another VM that will serve as the client. The image for this machine should be Windows 10, NOT Windows Server like I did for the previous machine:

![image](https://github.com/user-attachments/assets/7a2078cd-f157-4a11-b1e6-d73baf181aa0)

![image](https://github.com/user-attachments/assets/a147e0bd-5c5f-4edd-be04-614c147bf5d2)

In the Networking tab of this VM, I'll make sure it will create itself on the same virtual network of the previous machine created. I'll leave all other settings default and create this VM:

![image](https://github.com/user-attachments/assets/285853c9-00c2-40fc-b91e-aa4497093592)

I now need to set our DC (Domain Controller) private IP address to "static" as by default it is set to "dynamic". I want this to be static, because this DC will double as a DNS (Domain Name System) server, which I will tell our client to use as a DNS server later. If the IP allocation setting were set to dynamic, the IP address could change leaving the DNS configuration of our client invalid. So, I'll go to the network settings of the DC and switch the IP allocation to static:

![image](https://github.com/user-attachments/assets/e47c737b-e101-46f6-a3a5-f16942d008fc)

![image](https://github.com/user-attachments/assets/fb0fcf28-4b95-4cd6-bc89-f4be69d0cdb1)

Next, I'll use Remote Desktop Connection to connect to the DC using its public IP and the log in credentials I created when setting up this machine:

![image](https://github.com/user-attachments/assets/e43ab617-1899-40c3-8684-5ca5fee460d2)

Once I'm logged in, the following screen will appear with the Server Manager open. (If this isn't what you're seeing and instead it a regular windows desktop, you may have connected to the client VM instead or chose the wrong image when creating the DC):

![image](https://github.com/user-attachments/assets/ec106ef1-141e-4338-adcf-f4c6f625b7d3)

Next, I'm going to disable the firewall (you probably wouldn't do this in real lfe, but for the sake of this lab where nothing is at stake, I'll go ahead and do it). So, to disable the firewall I'll right click on the "Start" button and select "Run". Then type "wf.msc":

![image](https://github.com/user-attachments/assets/e70673c3-cb0f-40e9-8efe-575fec07b8bd)

Click on "Windows Defender Firewall Properties" then on the, "Domain Profile", "Private Profile, and the "Public Profile" tabs, turn the firewall state off:

![image](https://github.com/user-attachments/assets/385287d7-84c9-4a0b-b381-5a5c052b86f8)

![image](https://github.com/user-attachments/assets/e259ef55-8b0f-462c-bff6-526a863ab522)

![image](https://github.com/user-attachments/assets/0ffc0320-c551-4267-a37c-e4320693088b)

You should see that all the firewall settings are disable:

![image](https://github.com/user-attachments/assets/0ba52e60-cd34-46c9-8cda-84d6ac14c3d2)

Next, I need configure our clients DNS settings to the DC. To start, back in Azure, I'll grab the DCs private IP address:

![image](https://github.com/user-attachments/assets/5dfcf864-7b68-440c-a329-15efd203fca8)

Then, I'll go to the network setting of the client machine. click on the NIC (Network Interface Card), go to settings, then DNS servers and switch from "Inherit from virtual network" to "Custom". Input the DCs private IP here and save:

![image](https://github.com/user-attachments/assets/1bd9e2c4-f11c-47a8-b044-b70a100b4d29)

After that's saved, I'll restart the client machine:

![image](https://github.com/user-attachments/assets/3e1b0835-8045-4351-8c13-f43c68b25ce5)

Once the machine as restarted, I'll use Remote Desktop connection to connect to the client machine using its public IP and the log in credentials I created while setting up this machine:

![image](https://github.com/user-attachments/assets/af9ea637-2d14-4398-b98c-d894dd4b8142)

Now that I'm logged in, I will open Powershell and attempt to ping the DC using the ping command and its private IP address. In my case it'll look like this. (If there is an error and the connection timed out, double check in Azure to make sure both of the machines are on the same virtual network. If they aren't this is likely causing the error and you'll need to set up the machine again on the same network):

![image](https://github.com/user-attachments/assets/e4b8d63f-8648-469f-922d-7cc4f0394e0f)

While I'm here I can double check that the DNS server settings are pointing to the DC. I'll run "ipconfig /all" and look for the "DNS Servers" and it should point to our DC if everything is set up properly:

![image](https://github.com/user-attachments/assets/d8b9da51-a8e0-4c3a-ae55-87a679c970b6)

<h2>Active Directory Infrastructure is Now Prepared! </h2>

<b>We've successfully created two VMs (Virtual Machines), one running Windows Server, to act as a Domain Controller. The other VM as a client, running Windows 10. Don't forget: In later projects I will deploy AD, run a script that will create users in the domain, which I can log into from the client VM, then manage the accounts and update the group policies, all to simulate a real life environment!</b>
<br />
