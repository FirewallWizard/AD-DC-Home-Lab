# AD-DC-Home-Lab

![image](https://github.com/user-attachments/assets/d19559e5-e748-40a9-93e0-7e22bddc40e2)


## Objective
The project aimed to set up a fully functional Active Directory Domain Controller (ADDC) environment using VirtualBox, creating a private network with simulated users and clients for lab-based study and cybersecurity practice. The focus was on deploying Windows Server 2022 as a Domain Controller and configuring features like NAT, DHCP, and Active Directory, culminating in a Windows 10 client setup connected to the domain. This environment serves as a foundation for practicing network administration, user management, and eventual penetration testing.

### Skills Learned
- In-depth knowledge of Active Directory Domain Controller (ADDC) configuration and deployment.
- Familiarity with Windows Server and Windows 10 networking setup, including IP addressing and NAT configuration.
- Proficiency in configuring DHCP for automated IP allocation in a domain environment.
- Hands-on experience with PowerShell scripting to automate user creation in Active Directory.
- Understanding of virtual networking concepts and use of multiple network adapters in VirtualBox.
- Strengthened skills in diagnosing and troubleshooting network connectivity and security settings.

### Tools Used
- Oracle VirtualBox for virtual environment creation and configuration.
- Windows Server 2022 and Windows 10 ISOs for virtual machine OS installations.
- PowerShell for Active Directory user management and automation scripting.
- Active Directory Domain Services (ADDS) for domain creation and user management.
- Routing and Remote Access Service (RRAS) for NAT configuration and internet routing.
- DHCP server in Windows Server for dynamic IP addressing within the private network.

## Steps

Install:
I downloaded VirtualBox and the extension pack here. After VirtualBox is installed, download Windows 10 ISO. I used the media-creating tool and followed the prompts to create an ISO file.

![image](https://github.com/user-attachments/assets/7f736c2f-25db-42fa-a1d5-b68e3bc21aeb)


Then I downloaded Windows Server 2022 and downloaded the 64-bit edition ISO.

I set up the domain controller in VirtualBox by clicking New -> Naming my 2022 Server ISO, DC.

![image](https://github.com/user-attachments/assets/4a67e313-5f33-4b92-8d6e-38e18faaa4c6)

![image](https://github.com/user-attachments/assets/3aeda872-52e0-49e1-9843-b4ed269212e0)

![image](https://github.com/user-attachments/assets/fae6b4d1-368e-43c7-a412-5d8939cb2852)


Next, I clicked on Settings to change a couple of things. I went to advanced and made Shared Clipboard and Drag and Drop Bidirectional so that I could copy and paste between my desktop and the server.

Then I switched over to the Network tab. According to the diagram, we want two NICs. One dedicated to the internet will be running NAT, and then one dedicated to the internal VM network. Our first adapter is NAT by default. I went to adapter 2 and added the internal network.

![image](https://github.com/user-attachments/assets/e6008b64-620c-45c5-a7a7-4aaf5138c0f9)

![image](https://github.com/user-attachments/assets/6861d90b-4eee-4457-a196-e675291a61b0)


The domain controller is now ready to be started.

![image](https://github.com/user-attachments/assets/cf84daae-640b-4fe1-ae67-406e543ad9d8)

I chose custom install, chose drive 0, and started installing Windows.

![image](https://github.com/user-attachments/assets/2c68cd10-1d06-4d72-820b-3625380dceec)

For the administrator password, I chose password1 for this example for simplicity. This isn’t best practice and I will be frequently adding and changing things in this lab as I use it to study for my OSCP and beyond.

![image](https://github.com/user-attachments/assets/4f9f1894-f9c8-4d78-93b2-e683f7435c32)

Windows asks me to input Ctrl + Alt + Delete, but is an issue since we are on a VM. To work around this, you go to View > Keyboard > Insert Ctrl + Alt + Delete

The very first thing to do is install Guest Additions by clicking Devices > “Insert Guest Addition then going to the folder, double-clicking, running amdx64, clicking through all the prompts then restarting Virtual Machine.

![image](https://github.com/user-attachments/assets/627b2f77-a7dc-4794-8bee-1929590279b9)

Next Step is to set up our IP Addressing. According to the diagram, there are two NICS. One for the internet and one for the internal. The internal has to be set up manually. Once the VM is restarted go to the network and intern > network connections.

Go to Status Details to confirm which one is internal. If the autoconfiguration is 169, then it is automatically assigned. That’s how we know that it is internal.

![image](https://github.com/user-attachments/assets/008680d3-1dda-4d8e-ba3b-d077787ea6d4)

The next step is to rename the PC. Right-click menu > System > Then Remain PC from something arbitrary to DC-22.

![image](https://github.com/user-attachments/assets/7e281fd9-08f4-4cef-ac40-93d5c39c0239)

The next step is to give an IP address, before restarting. Click on Network, go to Private, go to Properties, and change IPv4. Assign IPv4 address to internal to 172.16.0.1 and Subnet Mask to 255.255.255.0. There will be no default gateway since the domain controller itself is serving as the internal gateway. For the DNS server, it automatically installs DNS.

![image](https://github.com/user-attachments/assets/50c8bc1a-6518-48eb-8ec0-66f7755f3494)

The next step is to install Active Directory Domain Services and create a Domain. Go to Server Manager and add roles and features, Next, Next, and then pick the server that you want to install AD to.

![image](https://github.com/user-attachments/assets/60be8dbe-ba9e-4525-879e-7a32b686b7e4)

![image](https://github.com/user-attachments/assets/4f8d2e93-ba42-465c-9427-c1c57b90dbd3)


After the role is installed click the yellow flag at the top right to deploy post-deployment configuration. This will allow us to promote the computer to a Domain. For this example, this domain has been changed to mydomain.com

![image](https://github.com/user-attachments/assets/f8b43dc0-d07b-4381-ac56-ee8851e84c3d)

Click next on the following prompts and Install. The next step is to create a dedicated domain admin account instead of using the built-in account. Go to active directory users and computers from Windows, go to my domain, and new. Create a new Organization Unit. An OU is a folder in an active directory.

![image](https://github.com/user-attachments/assets/3a6bc3be-a9e0-4e63-8d80-b8d757d121b3)

Inside of admins, create a new user. The common naming convention can be the first letter of the first name and last name

![image](https://github.com/user-attachments/assets/07e6b447-0c0f-4ead-a8e2-8ff9ce1fec2e)

Confirm the password, and you will see an account, although it's not an admin even though it's named that way. Go to properties, go to members of, and add it to Domain Admins. Apply and now we have out own domain admin account

![image](https://github.com/user-attachments/assets/2d7167de-55ce-45b3-a944-f5bb656b2a5c)

To use this, log out, and log in. Instead of logging into the administrator, log in to another user and log into the domain admin account.

‍The next thing to do is to install RAS / NAT. The purpose of this is to allow the created client to be on the private network but still be able to access the internet through the domain controller. In order to do this, go to add roles and features. For roles, add Remote Access and install Routing.

![image](https://github.com/user-attachments/assets/eec05b2e-be69-42b1-81bb-99ebdb6f440f)
![image](https://github.com/user-attachments/assets/57367e79-41c1-43cf-8621-be47ed142687)


Then go to Tools > Routing and Remote Access > DC Local > Right Click >Configure and Enable Routing and Remote Access. Install NAT to allow Internal clients to connect to the Internet using one public IP Address.

![image](https://github.com/user-attachments/assets/550acff0-5504-44e9-a83a-cbe08deb75e6)

Then use the one named Internet to connect to the Internet, click next then finish.

![image](https://github.com/user-attachments/assets/5d8aef0e-f0ae-4599-ac17-ef74a5384448)

Now Windows 10 Clients can connect to the internet. The next step is to set up a DHCP Server. What this does is allow Windows 10 clients to get an address and then connect to the internet. Go to add roles > next > and select DHCP Server. Go to Tools > DHCP and set up our scope.

![image](https://github.com/user-attachments/assets/9fce2d30-8093-4e24-9e9f-583bc947806c)

Click on IPv4, right-click, new scope. I named the scope after the IP range.

![image](https://github.com/user-attachments/assets/1bbbb25b-6379-4ef1-bb16-aa85cdd1b9fa)

![image](https://github.com/user-attachments/assets/4ee4c4d7-c5cd-4d1b-a28d-f6761f5ff727)

The user duration is set for the use case. If you have an internet café then you don’t want an internet café with a default of 8 days, might want to change it to 2 hours instead of 8 days. 8 days is good for the lab. Yes to configure. NAT is configured on the Domain Controller so that’s what we want the clients to use. The IP address will be 172.16.0.1.

![image](https://github.com/user-attachments/assets/ce0cea4f-cd5c-4c8e-a09d-e06a3f3ea649)

The domain controller needs to be used as a DNS server. Right-click dc.mydomain.com to authorize then refresh. The next step is to make a configuration to browse the internet. Usually, you don’t want to do this in a production environment. We will be disabling Internet Explorer enhanced security.

Go to Server Manager > Local Server

![image](https://github.com/user-attachments/assets/55cdce01-a625-4fc1-9dc0-f0693f406242)

I downloaded the script in PowerShell to load tons of users to simulate employees in an organization

![image](https://github.com/user-attachments/assets/c12ce541-5da3-405f-92cf-8acb34f22a08)

So this script includes a names.txt which has 1000 randomized names. At the top add your name‍.

![image](https://github.com/user-attachments/assets/5cba7652-63b5-4a21-86aa-521a4d8661c0)

Next, open Windows PowerShell ISE as an administrator.

![image](https://github.com/user-attachments/assets/95086528-dd2f-4598-b4c0-241c76500cb1)

Open the PowerShell script. _CREATE_USERS.ps1

Before anything is run you have to enable the execution of all scripts. To get around this, set the execution policy to unrestricted.

![image](https://github.com/user-attachments/assets/36cd7884-8b55-4f6a-860c-869b78bba7b4)

An Overview of the script:

At the top, all users are using the same password of Password1
The GetContent with the names.txt essentially means that it is taking all the names and copying and pasting them into the variable as an array
The password = variable takes the plain password and creates it into an object so PowerShell can use it as a secure password
The NEW-ADOrganizationalUnit line creates another OU with _Users instead of _ADMINS
Each takes users first and the last list takes the users and the code runs for each user
First splits the line from the space and takes element 0 in the first name
Last splits the line from the space and takes element 1 in the last name
Username takes substring -> the first letter of the first name and glues it to the last name in lowercase
Line 13: Outputs to screen in Cyan create an alert that a user is being created
Lines 15 to 24 create the new user in the active directory
Go to the actual directory where the script is (I saved it to my desktop) and run the script

![image](https://github.com/user-attachments/assets/3c22f3f2-8d0c-45aa-8d29-4c02d9e50f41)

![image](https://github.com/user-attachments/assets/612716f0-8c95-4c87-97dc-2049153b9a54)

You should be able to find your user. If you search your last name you should find both

![image](https://github.com/user-attachments/assets/75e4a816-8b7b-4c9f-899a-d5380029613c)

Now that we have users created and our entire environment, almost everything is set up. The very last thing is to create a Windows 10 VM. Go to the virtual box and create a new VM

‍![image](https://github.com/user-attachments/assets/355cb17f-0d97-461f-a35d-5d990be321a4)

Repeat by going to settings, enabling bidirectional. For network, instead of using NAT, use an Internal Network so we can get a DHCP address to emulate an organization

![image](https://github.com/user-attachments/assets/83fe92f5-975d-4b1a-b3e7-ab9523ee706d)

For the Operating system choose Windows 10 Pro so you can join it to a domain.

![image](https://github.com/user-attachments/assets/73dd89d2-2c58-4fd0-b751-5e997bde275d)

Go through the prompts > Next > Next > Skip for the second keyboard layout

‍![image](https://github.com/user-attachments/assets/e7422021-8ce8-429c-833e-26a4c0ce121e)

Install with a limited setup to avoid making a Microsoft account.

The local username will be User and no password. Turn all custom experiences off. The first thing to do is make sure the internet is working.

![image](https://github.com/user-attachments/assets/052e8e96-c2af-4a8d-a248-106262de9ef7)

![image](https://github.com/user-attachments/assets/ade83ad0-71b5-4a2d-a007-a1f366a12fb5)


Because we can ping google.com we can confirm that the internet is being NATed through the DC and to the internet and back

![image](https://github.com/user-attachments/assets/703172ad-11f7-4e41-90cb-0778daf5d32e)

We can also pingmydomain.com.

Go to Windows >System > Scroll all the way down to Rename this PC advanced

![image](https://github.com/user-attachments/assets/7cd343a6-795e-491c-8867-2fe493e54f9c)

This is so we can join the domain at the same time. Click Change and name it to client1

![image](https://github.com/user-attachments/assets/21764b92-a52f-43c1-b92b-2d2deec233d6)

Use the admin password from earlier to log in and join this client to the domain

![image](https://github.com/user-attachments/assets/7503d0c8-0461-4d04-9a53-4ed29df6f585)

Another way to see this visually is to go to your DC and go to Tools > DHCP > Scope> Address Leases to see that the IP has been leased.

![image](https://github.com/user-attachments/assets/5bc61c71-2140-442d-929c-bf79be5b3954)

Next, you can log into a created user from the script.

![image](https://github.com/user-attachments/assets/df44b1c2-d753-41db-ab9e-0ca4c7d46703)

This concludes with setting up an Active Directory Domain. I plan on continually adapting this to have different vulnerabilities to emulate a penetration test attack in the future.
