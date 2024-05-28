<img width="697" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/51645b26-b4e7-4842-b0ed-325a2ca98d4e"><br><br>
- - - -

<h1>Active Directory Deployed in the Cloud (Azure)</h1>

This tutorial outlines the implementation of Active Directory within Azure Virtual Machines.<br>

<h2>Environments and Technologies Used</h2>

* Microsoft Azure (Virtual Machines/Compute)
* Remote Desktop<br>
* Active Directory Domain Services<br>
* PowerShell<br>

<h2>Operating Systems Used</h2>

* Windows Server 2022<br>
* Windows 10 (22H2)<br>
<h2>High-Level Deployment and Configuration Steps</h2>

* Setup Resources in Azure<br>
* Set Domain Controller’s NIC Private IP address to static<br>
* Ensure Connectivity between the client and Domain Controller<br>
* Enable ICMPv4 in on the local windows Firewall and check connectivity<br>
* Install Active Directory<br>
* Create an Admin and Normal User Account in Active Directory<br>
* Join Client-1 to your domain<br>
* Setup Remote Desktop for non-administrative users on Client-1<br>
* Create additional users and log into client-1 with one of the users<br>
* Deployment and Configuration Steps
<h2>Deployment and Configuration Steps</h2>
Create the Domain Controller VM (Windows Server 2022) named 'DC-1'. Take note of the Resource Group ( which can be created on the VM creation page or from the Resource Group creation page ) and Virtual Network (Vnet) that gets created here. Select a Region to place both VMs and then select a size that has atleast 2 vcpus; anything less will cause the operating system on the VM to run slowly. Once everything is filled out, review and create the VM.<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/260be658-094f-4dab-bfb9-f9311631ff13"><br><br>

- - - -

First, go to Network Settings -><br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/7369b210-8923-4fae-8535-7ceaa0d1033b"><br><br>
NIC -><br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/fef51dd1-3c48-4218-8d63-2b6de40b0488"><br><br>
IP configuration -><br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/b9c85807-31ba-4e09-afcb-d56f6da433cf"><br><br>
Then on the Edit IP configuration page, change the allocation from Dynamic to Static and select save.<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/9875e834-f848-47ba-84fc-d7bab4cb60d1"><br><br>

Next, create the second VM named 'Client-1'. Make sure to use the same Resource group as DC-1. Check 'licensing' then select Next: Disks and then Next: Networking to reach the networking page.<br>

Ensure that both VMs are in the same Vnet. (NOTE: if the Vnet from DC-1 isnt showing in the drop down menu, then allow more time for the Vnet to fully deploy before creating Client-1 VM)<br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/c1978be4-d245-4b76-8606-a5db6c52d259"><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/8f1fe94e-277e-44fa-bf67-2199322fc3aa"><br><br>
Once the Vnet is verified, select Review + create and if the validation passes the VM can be created.<br><br>
- - - -

Next, ensure connectivity between the client and Domain Controller.<br><br>
Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t (DC-1 private ip address)<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/38c0f730-9b48-49ca-be87-e0d109fce4ef"><br><br>
<img width="471" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/812d272c-5ce8-4061-a9fa-d7d62dbb20b4"><br><br>

The ping will time out so you'll need to start another instance of remote desktop and login to the Domain Controller to enable ICMPv4 in on the local windows Firewall.<br><br>

In the search bar in DC-1, type wf.msc to open Microsoft Management Console Document -> Inbound Rules<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/b7339af2-e14f-429b-879e-717bb24765c2"><br><br>
Sort by protocol -> ICMPv4 -><br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/8de70d08-3d5c-42d8-a9b2-8d1907130aa3"><br><br>
Right click to enable Core Network Diagnosis rules.<br><br>

Return to Client-1 and the ping should now be responding. <br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/36532058-cf06-4f9e-97be-f67756e132dc"><br><br>
Next step is to install Active Directory.<br><br>
- - - - 
First, switch back to DC-1. Then go to Server Manager -> Add roles and features -> Active Directory Domain Services<br><br>

(NOTE: Make sure to only select Active Directory Domain Services as there are multiple Active Directory available)<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/6dca0038-7695-4079-bc87-62e61dd46fd2"><br><br>
<img width="750" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/b4fb92e7-3dca-4d39-ac33-170afd5deb64"><br><br>
Add features and select next and install.<br><br>
- - - -

Next, promote the server as a Domain Controller. In the upper right corner there will be a flag with an action sign next to it in the Server Manager notifications section. Select promote this server as a domain controller.

<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/3fff1813-ee92-4114-bbb4-e0a3ac43c72c"><br><br>
Select add new forest then set up a new forest as mydomain.com. Conversely it can be named anything, just remember what you decide to use.<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/56399e71-6415-4736-b3dc-1e6e04cc32df"><br><br>
Choose a password on the next page and click through 'Next' and install. Once the installation is complete the host will restart.<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/e0765bf7-50ad-47cd-bdd0-454415eefd01"><br><br>
Return to DC-1 in Azure and refesh the VM so the public ip address updates, then log back into the remote desktop. The ip address should be the same but it might change upon refresh. DC-1 is now a domain controller, so you'll have to log in with the context of the fully qualifed domain name (FQDN) as username. So: mydomain.com\whatever username thats a member of this domain, which you created when you initally set up the VM.

i.e.: mydomain.com\labuser<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/9ef33876-2d71-42eb-a504-c1b7258c89f7"><br><br>
- - - -

Now you'll create an Admin and Normal User Account in Active Directory.<br><br>

From the search bar on DC-1, locate Active Directory Users and Computers then right click to create an Organizational Unit (OU) named '_EMPLOYEES' (the underscore and caps are not necessary, but they do help to eaisly differentiate the OU's created from the other containers) and another OU named '_ADMINS' then simply right click on the domain to refresh.<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/b76e3b37-8c33-49c2-bbf0-80a0b3dccd71"><br><br>
Next go to the _ADMINS OU, open it up and right click inside for New -> Users and add new user. Fill in the name and create a password.<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/1274265f-b1b5-44e6-816d-e975334b4042"><br><br>
Add your new user to the Domains Admins Security Group to make them a domain admin. Right click the user to open properties -> Member of -> Add and type 'domain admins' in the box and select Check Names and it should underline, then apply your changes and select ok. <br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/822bb784-3acb-4353-a708-350e8318ea48"><br><br>
Log out of DC-1, reopen Remote Desktop and log back in as the newly created admin user.<br><br>

i.e.: mydomain.com\jane_admin<br>
The next step is to join Client-1 to your domain.<br><br>
- - - -

From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address.<br>
Select Client-1 -> Network Settings -><br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/cd36ab66-344c-4f39-92e3-1a18a7c49980"><br><br>
NIC -><br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/39029c94-d68c-46b2-b984-11e46cb63ca9"><br><br>
DNS servers -><br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/d39620d2-5b77-487c-aef4-24968c12e83d"><br><br>
Custom -><br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/5ad73b36-586c-4fe1-bffb-3de49f0fcdea"><br><br>
Enter DC-1's private ip and save.<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/70dd3344-bc79-4e11-b5a5-ba4984046859"><br><br>
Now from the Azure portal, restart Client-1.<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/170e0b76-8a02-4ad2-a00b-59983000072d"><br><br>
Next, login to Client-1 (Remote Desktop) as the original local admin (original username only, same domain name) and join it to the domain.<br><br>

Right click start -> System -><br><br>
<img width="250" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/f0210c2d-ef3c-4022-bb06-d7f4d102ade8"><br><br>
Rename this pc advanced -><br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/0d61a73b-17b4-481a-a677-b0230f30aa89"><br><br>
Change -><br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/cfdb69e0-c54a-48cb-8d65-cdfbe8623b06"><br><br>
Domain -><br><br>
<img width="410" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/0fc6baf9-0215-4b84-a4fa-9c0dd3e435c5"><br><br>
Enter the domain name you created in the box (i.e.: mydomain.com) -><br><br>

Enter the credentials of the domain admin account -> mydomain.com(admin user) -> enter admin users password. Welcome to (domainaddress).com should populate somewhere behind the open windows.<br><br>
<img width="350" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/a69b9f70-018b-4348-a297-92f23436353a"><br><br>
The host will need to restart.<br><br>

Next, login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the 'Computers' container on the root of the domain.<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/4e2ef510-9e08-4cc3-91dd-4cc4603c268d"><br><br>
- - - - 

Now you're going to set it up where all normal domain users are able to remote into Client-1. As of now, only the administrative accounts have remote access into Client-1.
So log into Client-1 as the domain admin account (i.e.: mydomain.com\jane_admin), go back to system properties (right click the Start menu and select 'System'). Then select Remote Desktop -><br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/3377f9be-4f06-41e3-924c-4ed0125e9e3c"><br><br>
Select users that can remotely access this PC -> Add... -><br><br>
<img width="461" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/df46b3a8-a9ad-43bd-8075-7fd98d5670b6"><br><br>
Type 'domain users" in the box -> Check names -> Ok.<br><br>
<img width="461" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/4d357428-8e70-42d9-9d18-de12e4db4cd0"><br><br>
Essentially now all domain users are allowed to log in to this computer. Now you can log into Client-1 as a normal, non-administrative user.<br><br>
- - - -

Lastly, you will create a bunch of additional users and attempt to log into Client-1 as one of the new users.<br><br>

Login in DC-1 as your admin account and open PowerShell_ISE as an administrator.<br><br>
<img width="515" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/05e8b14f-0f69-4ab7-8761-8fb1a1864b4d"><br><br>
Select new script in the upper left corner -><br><br>
<img width="515" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/4a7d9090-2696-442f-b04d-61c708e3d8d4"><br><br>
Paste the contents of your name generator script into Powershell and run the script.<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/f0238555-5608-41be-989f-407453c00fee"><br><br>
Observe the accounts being created<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/9b3371f8-5d71-4498-9ddf-59f974709867"><br><br>
Once complete, open Active Directory Users and Computers and refresh the '_EMPLOYEES' container. Observe the newly created accounts.<br><br>
<img width="500" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/0b4d03e8-a6cb-4af4-9050-253e0b31c033"><br><br>
Now attempt to log into Client-1 with one of the newly added accounts using the password from the script.<br><br>
<img width="426" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/78c5b8bb-4211-4c42-b74f-b22f99cb1b19"><br><br>
<img width="467" alt="image" src="https://github.com/nkgarrett/Configure-AD/assets/156832893/aa14f139-26a8-4bd9-9c60-109d8d122155"><br><br>
Active directly has successfully been deployed!




















 
