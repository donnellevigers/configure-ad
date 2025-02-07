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

![image](https://github.com/user-attachments/assets/10c6d601-3d4a-4a29-bcc9-25797d2024c9)
![image](https://github.com/user-attachments/assets/11793eed-5c57-43aa-85fb-98aa2890611d)
![image](https://github.com/user-attachments/assets/15f28e9c-2c2e-4f07-9aca-fca11e0a98ed)

I then went and got the public IP for the client machine and added it to my remote desktop app and logged in. 

![image](https://github.com/user-attachments/assets/4dd1015d-6bf5-47b4-8073-60abcd2fe904)

Once logged in, I opened Powershell from the Windows search bar and pinged the domain controller's IP to ensure connectivity. I also used the command ipconfig /all to ensure that the client was looking to the domain controller as it's DNS server. Where it says "DNS Server", that IP address should be the same as the domain controller. 

<h2> Deploying Active Directory </h2>

Next, I logged into the domain controller to deploy Active Directory. 

![image](https://github.com/user-attachments/assets/1590553f-80ff-496d-b926-8f0ffb7b7a95)
![image](https://github.com/user-attachments/assets/b3e8d33e-25ea-4709-9c11-3bb2e8142ef1)
![image](https://github.com/user-attachments/assets/79024c9b-8c3d-454f-8264-cc92476b5cab)
![image](https://github.com/user-attachments/assets/2d44a48e-3117-45da-8575-36072cf6aefc)
![image](https://github.com/user-attachments/assets/b5f27674-6d57-4bf2-89fd-5039a73189a8)


First, I opened the server manager and clicked on "Add roles and features". From here, I clicked next all the way to "Server Collection", confirmed the IP address was the same as the domain controller, clicked next to "Server Roles", then checked the box for "Active Directory Domain Services" and continued press next until I was able to install. 

![image](https://github.com/user-attachments/assets/349aea4e-0fd6-447c-891c-5019c5792723)
![image](https://github.com/user-attachments/assets/cc40c5dc-221f-44dc-a038-1f82687dc1ed)
![image](https://github.com/user-attachments/assets/183189e3-8d19-47d5-924f-1de5e83e00cf)

After it is in installed, I clicked the yellow flag on the top right to officially promote dc-1 to a domain controller. I added a new forest with the domain name "mydomain.com". Next, I made a password. I then pressed next until I was able to install. After installation, I am now able to login to the domain "mydomain.com" with remote desktop through the domain controller machine using the domain + \dc1 (mydomain.com\dc1 as the username).

![image](https://github.com/user-attachments/assets/33d33c47-94ce-4a0b-8ec2-150dcb635be1)
![image](https://github.com/user-attachments/assets/945709a5-ac52-427f-86ea-3217f71f4791)
![image](https://github.com/user-attachments/assets/544d2bd9-92a4-4c9a-b6e6-213096c4b5c5)
![image](https://github.com/user-attachments/assets/fc1034cd-2f4c-4b89-aef5-f8c525068563)

From the Windows search bar, I opened "Active Directory Users and Computers". I right clicked on the "mydomain.com" and created two new organizational unit named "_EMPLOYEES" and "_ADMINS". These will be useful for when we add users to Active Directory. 

![image](https://github.com/user-attachments/assets/8c01bcfe-0785-4685-a89e-83aa5af4df44)
![image](https://github.com/user-attachments/assets/707bbf6d-e413-4dff-a63c-41d4ba9287e1)
![image](https://github.com/user-attachments/assets/0687a9bb-2eb1-40b9-8d99-2a26a1fcc8b4)
![image](https://github.com/user-attachments/assets/c884324d-7212-4817-a92d-236f01d5e496)
![image](https://github.com/user-attachments/assets/851d5cfb-a24f-4d2c-94db-aaad908a20ab)
![image](https://github.com/user-attachments/assets/0b77023f-f9e4-4775-b87a-a5432e073e42)

Next, I opened the _ADMINS unit I just created and created a new user named Jane Doe with the username "jane_admin" along with a password. Although Jane has been added to admin group, it does not mean they have admin access. I right clicked on their name, hit properties then member of, add, and then typed in "domain admins", check names and that will underline domain admins. Active Directory has a built-in admin access profile which makes it easy to add admins without having to too much tinkering. Of course, you'd want to tailor it to the business' needs. 

![image](https://github.com/user-attachments/assets/7d747ce6-d093-4ca0-b159-ad28804c0681)
![image](https://github.com/user-attachments/assets/644193ba-0a5e-497e-9215-f6552fcce2fd)
![image](https://github.com/user-attachments/assets/2295df9f-60d5-4505-bb4e-7d08290f3168)
![image](https://github.com/user-attachments/assets/a949cd7f-fccc-41dd-a370-a1957f14d24d)

I then logged into client-1 using Jane's login. The next step will add client-1 as an official member of the domain "mydomain.com". I went to "System" from the Windows logo, hit "Rename this PC", "Change", and added "mydomain.com" in the domain field. It'll ask for permission and all that's needed is Jane's login information. After this is finished, the computer will need to restart to finalize the changes. 

 ![image](https://github.com/user-attachments/assets/7d02ef76-9bf4-40a9-8688-640a66fc7aa0)
![image](https://github.com/user-attachments/assets/187a8e09-12eb-4653-8d55-7fd84e44cd42)

To check my work, I logged into dc-1, opened Active Directory Users and Computers, went to computer and saw that client-1 was added. Success! To be more oraganized, I created a new organizational unit named "_CLIENTS" and dragged the client-1 computer there. 

![image](https://github.com/user-attachments/assets/54c6f869-10f6-4c2f-ba7a-b4bd49fd51d0)
![image](https://github.com/user-attachments/assets/f1c3a425-1865-419b-bace-2315bb5b7bee)

Next order of business is allowing users access to remote desktop services. I logged into client-1 as jane_admin, opened system settings, clicked on "Remote Desktop", "Advanced Settings", "Add", and added "domain users" to the field. 

![image](https://github.com/user-attachments/assets/4d6d8751-b62f-438e-982d-561b5c00f607)
![image](https://github.com/user-attachments/assets/02e792e6-a5c9-41c5-bc9b-79aebaa49523)
![image](https://github.com/user-attachments/assets/5bf562c0-6399-4817-b105-a63accabfed7)

Now I would like to add users to the _EMPLOYEES group to simulate a business with 50 users. I copied a script made to make 10000 and modified it to 50 users. I ran Powershell ISE as an administrator, started a new script and saved it, and ran the script. 

![image](https://github.com/user-attachments/assets/524ab654-de12-4486-81d9-34dee04ff1b0)
![image](https://github.com/user-attachments/assets/d11c2f73-fc6b-4415-8974-5681d530653a)

I opened Active Directory Users and Computers, went to the _EMPLOYEES group and found a user. I logged out and logged in with that users credentials using mydomain.com\vid.map (mydomain.com\ + username) and the password found in the script ran in the previous script. If it works, then you've done everything correctly!

<h2> Group Policy Management </h2>

Since we have users, we can now explore a few things you can do in Active Directory like Group Policy Management. With this, you are able to set rules for users such as how many times an account locks out after a certain amount of failed log-in attempts and what computers are allowed to be connected to remotely or not. 

![image](https://github.com/user-attachments/assets/7703bb2f-c778-4b82-ad00-30b890e259cc)
![image](https://github.com/user-attachments/assets/1a5aaa83-6567-4c29-b4b7-afcba6921908)
![image](https://github.com/user-attachments/assets/2571d36e-79b1-43a1-a561-4fe9259312f3)

In the dc-1 machine, I opened the Group Policy Management app. I hit the dropdown arrows for the forest, domains, mydomain.com, and there is a default policy there. I right-clicked and edited it. From here, I hit the dropdown arrows for Computer Configuration, Windows Settings, Security Settings, and lastly hit Account Lockout Policy. Here there are different options for accounts like how many times a user can fail logging in before a lockout and the duration of the lockout. You are able to view all the attributes of the policy by clicking on the policy. You will see that there are so many options! It seems endless!

![image](https://github.com/user-attachments/assets/0b172f97-af1d-4f6c-a069-ad9df9dddc2d)
![image](https://github.com/user-attachments/assets/7b58c71b-2dec-4be2-8f7f-d399f022490d)
![image](https://github.com/user-attachments/assets/07dc523f-eda0-4c06-a951-b43c5914432d)
![image](https://github.com/user-attachments/assets/bb566329-4145-4166-a8dc-94afd1cb7fe1)

Going back to Users and Computers, I right clicked on a user in _EMPLOYEES to find that you can disable, enable, lock, unlock and reset passwords.

<h2> Event Logs </h2>

![image](https://github.com/user-attachments/assets/9a92d174-4b78-4844-82fd-604e3aaae0a2)
![image](https://github.com/user-attachments/assets/a88e1c3b-bdd5-4f8e-97fc-6071268c4884)

If I ever had the need to look for suspicious activity, I can open the Event Viewer application where it'll show logs such as when a user tries to log in and it's suceeded or failed, when and account gets logged out and so much more. 

There is much you can do with Azure and Active Directory and these skills transfer to other platforms that deal with user databases. Learning these concepts will be able to provide a solid foundation for those in the information technology field. 










