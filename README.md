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
- Group Policy Management

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Set up Windows Virtual Machine and Domain Controller in Azure
- Install Active Directory
- Create an admin user within the domain
- Join client-1 to the domain
- Set up remote desktop access for nonadministrative users
- Create users to simulate an organization with employees
- Create Group Policy to create rules for accounts
- Explore ways to deal with accounts like account lockouts and password resets
- Observe event logs for domain controller and client vm


<h2>Deployment and Configuration Steps</h2>


![image](https://github.com/user-attachments/assets/02215f2d-d99b-43dd-906d-74de49977c57)

To get started, I created a virtual machine which will be the client machine and created a new resource group called Active-Directory-Lab. This will be the resource group both the domain controller and the Windows virtual machine will be in. 

![image](https://github.com/user-attachments/assets/9a5b6eac-e906-45fb-b537-cb1b345ae326)

I named this machine "client-1" and set my region as (US) West 2 in Zone 3. For the client machine, I used the Windows 10 Pro, version 22H2 image (the type of machine you are creating). For the size, I selected the option with 2vcpus and 8GB RAM to ensure this project would run efficiently. 

![image](https://github.com/user-attachments/assets/f3769eff-7b3d-4f89-9ab2-b90a4eeeabb7)

I then created a user name and password to log into this virtual machine. Be sure to keep note of this password combination and click the checkbox at the bottom. At the top, head over to the networking tab. 

![image](https://github.com/user-attachments/assets/a4a777f0-e877-42e6-b06e-c0279aa693e9)

In this section, I created the virtual network. The client machine and domain controller will both be a part of this network. I named my network "Active-Directory-Lab". 

![image](https://github.com/user-attachments/assets/a09f11dc-ff0e-4d04-9e1f-9c0cb5b61b92)

![image](https://github.com/user-attachments/assets/e6078248-e3ad-437e-8699-35ffd0ed264e)

There will be a notification that pops up. I clicked it so I can see when the machine has been deployed. You will have to wait till it is finished before you start to create the domain controller because the resource group and virtual network will not be ready until it is finished deploying. 

![image](https://github.com/user-attachments/assets/1bcb2f1c-3406-4ad8-8811-bfcab98f8d88)

This step is crucial. I made sure these settings are all the same as the client machine except for the name of the machine and the image. So ensure the resource group, region, and zone are the exact ones used previously. For the name, I chose "dc-1" and for the image, Windows Server 2022 Datacenter. 

![image](https://github.com/user-attachments/assets/988be660-dbe8-46f4-b075-7ab4293090d2)

Like before, I created a username and password to be able to login to the machine. Be sure to take note of this password combination. 

![image](https://github.com/user-attachments/assets/24fd297e-83bb-4dbe-8f32-ee1487ce5160)

Another very important step. I made sure to use the same virtual network as the client machine. 

![image](https://github.com/user-attachments/assets/8fda60b7-0e1b-41b7-9200-df585f0fe190)

Deployment is complete!

![image](https://github.com/user-attachments/assets/01eeedeb-4389-44c1-b775-908b683b878e)

In the search bar, I searched for "resource groups" to view all of the machines and networks to ensure everything is there. It is always good to check your work. 

![image](https://github.com/user-attachments/assets/63049e84-7539-40f2-ae70-130534258a04)

This next step, I will be starting the process of connecting the two machines, enabling the client machine to look only to the domain controller for DNS queries and create the foundational work for deploying Active Directory. This will involve finding out the IP address of the domain controller, setting the domain controller's IP address as static, and setting the domain controller as the DNS server for the client machine. 

![image](https://github.com/user-attachments/assets/d16d6fbe-0a21-45bf-ae2a-e2690df3f0c5)
![image](https://github.com/user-attachments/assets/4fba83ae-8ca6-4164-a1c8-89536b5a2d0b)
![image](https://github.com/user-attachments/assets/2d15ec46-e2da-4cfa-b442-94ec5b5708ec)

I then searched "virtual machines". Here, you can see all of the virtual machines you have created on your account. I went to "dc-1" machine, pressed the dropdown networking tab, and clicked on the network interface. This is just like a Network Interface Card (NIC) but virtual! 

![image](https://github.com/user-attachments/assets/78b5ce17-e73a-428f-ba1e-13dbcdaf8d1d)

Sometimes IP addresses change and it is important that for a server/client relationship like this that the IP address does not change freely. That way, the client will have the domain controller to look to without problems. It is almost like how if you have a fridge with and you need milk. You know exactly where the fridge is to get the milk. If the fridge can just change locations, you would constantly worry about whether you can get the milk but, if it always at the same spot, then there are no worries. Anyways...I clicked on ipconfig, static, and pasted the private IP address of the domain controller. 

![image](https://github.com/user-attachments/assets/1712b816-0c13-4444-b6db-da09510ff06e)
![image](https://github.com/user-attachments/assets/95c4643e-215c-43a7-a5fe-6facdeb716f6)
![image](https://github.com/user-attachments/assets/6ac94a42-7cdc-4c5c-91aa-86fbb7229a3c)

Now, I have to set up the client machine to look to the DNS server. This starts similar to a couple steps above. I went to the virtual NIC of the client. I clicked on settings, DNS Servers, the custom bubble and pasted in the private IP of the DNS controller. Save!

![image](https://github.com/user-attachments/assets/3ea53a1c-2693-4a04-98c0-7574ba97006a)
![image](https://github.com/user-attachments/assets/1a889096-9297-402a-a064-c1b0d143ae84)
![image](https://github.com/user-attachments/assets/17d49ed7-101c-491a-8c35-e42fa7383b97)
![image](https://github.com/user-attachments/assets/8cc14f6f-f15f-4559-ac66-9e0dc0638e36)

Now I will log-in into the domain controller. I went back to my virtual machines and copied the public IP adress of the domain controller. I opened my remote desktop app, added the pc by pasting the IP address in the "PC name" field and a nickname "dc-1" in the "Friendly Name" field, double clicked to be able to login using the credentials I created earlier. Clicked login and I was in!

![image](https://github.com/user-attachments/assets/cad1c03b-e22a-461e-b881-7a9fe55b5539)
![image](https://github.com/user-attachments/assets/177f0679-9634-4358-8e24-e433164d74da)
![image](https://github.com/user-attachments/assets/3cb785ac-7888-4078-bbd8-97c4d305f4ec)
![image](https://github.com/user-attachments/assets/2cf29eaa-86c5-4eed-868a-6b52c8fe1b63)
![image](https://github.com/user-attachments/assets/e132a5d2-f47a-478d-92fc-082ea7fcb559)

Once logged in, I started to turn off the firewall so I could test connectivity between the machines. I pressed cmd+r (mac) to open the run window and typed "wf.msc", pressed "Windows Defender Firewall Properties", and turned off the firewall for the domain, private and public profiles. 










