<p align="center">
<img src="https://i.imgur.com/PniAhoV.png" alt="osTicket logo"/>
</p>

# Azure Installing an Active Directory.
This tutorial outlines how to install an active directory using azure. 
<br />


<h2>Video Demonstration</h2>

- ### [YouTube: Installation of Active Directory in Microsoft Azure Cloud](https://youtu.be/tTI7z6XsQfU)
<!-- - ### [Notion: I've Included Documentation of the whole process](https://quill-lunge-517.notion.site/Active-Directory-4519124dff2e470e834158552a47a807?pvs=4) -->

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- Windows Firewall

<h2>Operating Systems Used </h2>

- Windows 10</b> (21H2)
- Windows Server 2022</b> (21H2)

<h2>List of Prerequisites</h2>

- Microsoft Azure Free Account
- Understanding of Windows Ost
- Understanding how a domain controller interacts with a client.
- Understanding of the difference between dynamic and static DNS assignments.

<h2>Installation Steps</h2>

- Create a virtual network
- create an address space on that virtual network

<br>
<p align="center">
<img src="https://imgur.com/8OxefDk.png"/>
</p>

- Create the virtual machine - Server 2022
- use the same virtual network
- create a subnet for the Active Directory Domain Server + click save

<br>
<p align="center">
<img src="https://imgur.com/Xjafa7M.png"/>
</p>

<br>

- Set the Ip Address to static

<br>
<p align="center">
<img src="https://imgur.com/o7qAQm2.png"/>
</p>

<br>

- Create the windows 10 Pro virtual machine - The Client
- Remote Desktop into The Client Windows
- Remote Desktop into The Server
- try to Ping the Server from the windows 10
- Enable ipv6 on local firewall on The Server
    - Click the protocol tab to re-arrange by the protocol and enable all the IPv4 option
        - Enable - Core network diagnostics - ICMP echo request - profile (private)
        - Enable - Core network diagnostics - ICMP echo request - profile (Domain)

<br>
<p align="center">
<img src="https://imgur.com/njH9gwG.png"/>
</p>

<br>


- Once Ping is working - we have connectivity

<br>
<p align="center">
<img src="https://imgur.com/LgNrcYc.png"/>
</p>

<br>

- Now we will install the - Active Directory Domain Services
    - Add roles and features
    - next
    - next
    - Select Active Directory Domain Services + Add features
    - next
    - next
    - next
    - Install
- Once finished installing
- Go to the top menu, click the Flag
- on the Post deployment → Click (Promote this server to a domain controller)
- Add a new forest


<br>
<p align="center">
<img src="https://imgur.com/dlRx0I9.png"/>
</p>

<br>


- Type your domain name (eg mydomain.com) [giovannidomain.com](http://giovannidomain.com/)
- Type your password

<br>
<p align="center">
<img src="https://imgur.com/xXxuYk2.png"/>
</p>

<br>


- next
- NetBios will automatically be filled
- install and you will be automatically signed out

### Create an Admin and normal User account in active directory

- Now we will create our own dedicated Domain Admin Account instead of using the built in administrator account
- Tools
- Active Directory Users and Computers
- Right click your Domain name → new → Organizational Unit → name it “_ADMINS”
- Right click your Domain name → new → Organizational Unit → name it “_EMPLOYEES”
- refresh

<br>
<p align="center">
<img src="https://imgur.com/aCxf5hL.png"/>
</p>

<br>


- right click “_ADMINS” to create a new user


<br>
<p align="center">
<img src="https://imgur.com/H9UbNFx.png"/>
</p>

<br>


- create the user

<br>
<p align="center">
<img src="https://imgur.com/iM2xJTb.png"/>
</p>

<br>

- Password never expires


<br>
<p align="center">
<img src="https://imgur.com/1831C4f.png"/>
</p>

<br>

- Click the user you just created
- Select Member of
- Add
- Type in the box “Domain Ad”
- Apply
- So now we have our very own Domain Admin Account
- Now we will test our new admin account we just created
- Sign out
- Log back in as the new admin user account - “mydomain.com\newaccountyoucreated”. Use the “User logon name”, that we set when we created the user
- We will use this account as our “Administrator account” from now on

<br>
<p align="center">
<img src="https://imgur.com/0cclsuw.png"/>
</p>

<br>

### Configuring the client Windows 10 VM - To JOIN CLIENTS to your Domain

- Go to azure
- copy the server’s private ip address
- go to your client virtual machine (Windows 10)
- on the left side bar, click “network settings”
- then click the network interface
- on the left side bar, click “DNS servers”
- click “custom”
- paste the servers ip address here
- Save


<br>
<p align="center">
<img src="https://imgur.com/6Q7Uzx8.png"/>
</p>

<br>

- Then we will restart the client virtual machine
- click your client virtual machine → overview
- Restart

<br>
<p align="center">
<img src="https://imgur.com/K8VBdc6.png"/>
</p>

<br>


- Remote Desktop back into the client VM
- We want to connect it to our Domain Controller
- Two ways:
    - one:
        - type “run” in the search box
        - then type “sysdm.cpl”
        - enter
    - two:
        - Right click the windows icon
        - click system
        - scroll down to “rename this PC (advanced)”
- Computer name tab → change
- click the domain radio/circular button
- then type your domain name (eg. giovannidomain.com)
- then click okay
- Type the original Administrator login, the one you used the first time when you signed into the server or another way of saying it is that it’s the login information you created when you created the server virtual machine where the section was “**Administrator account”**

<br>
<p align="center">
<img src="https://imgur.com/q2wOIqB.png"/>
</p>

<br>

- You are in!
- So now restart the vm to make sure the changes take place

- Now we will check in active directory to make sure the client was added
- go to tools → then click “Active directory users and Computers”
- click your domain name → Computers
- create another “organizational unit” by right clicking your domain name → new → organizational unit → and name it “_CLIENTS”
- Drag and drop the new client from the computers into the “_CLIENTS” section

### Setup Remote desktop for non administrative users

- Log back into the client VM but this time, using the domain name and regular password of the admin (the second administrator account we’ve created). So instead of using the client login information, we will use the admin login information.
- (eg: username: [giovannidomain.com](http://giovannidomain.com/)\gioadmin)  - Client ip: 20.185.38.227
- Then once the windows boots up
- right click the windows logo at the bottom left
- go to “system”
- scroll down to “remote desktop”
- Under “User accounts” → click “Select users that can remotely access this pc”
- and type “domain users”
- This allows “domain users” access to remote desktop and you will now be able to log into the “Client” as a normal, “non-administrative” user


<br>
<p align="center">
<img src="https://imgur.com/XisCCsP.png"/>
</p>

<br>


- And now just like that, the client is connected to the domain users

 Create additional users and attempt to log into the “client” with one of the users

- log into the DC-1 (Domain controller) as your (second admin account) (gioadmin)
- Open active directory Users and Computers
- Create an organizational unit called “_EMPLOYEES”
- right click and create a “new” “user” inside of “_Employees”


<br>
<p align="center">
<img src="https://imgur.com/3Tl3Qxg.png"/>
</p>

<br>

- Log into the client using one of the user accounts you have created

<br>
<p align="center">
<img src="https://imgur.com/NGgch2k.png"/>
</p>

<br>


- Perfect!

### Creating 1000 users

- Open powershell_ISE as a administrator
- We will enable the execution of all scripts
- type in powershell “Set-ExecutionPolicy Unrestricted”
- Now we need to change directory into where the name is located
- we type “cd C:\Users\gioadmin\Desktop”
- If we type “ls” inside powershell, we should see the user file, that has all the names
- Then copy the Powershell script code into your powershell


Powershell script:
```
# ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password0111"
$USER_FIRST_LAST_LIST = Get-Content .\names.txt
# ------------------------------------------------------ #

$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force
New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false

foreach ($n in $USER_FIRST_LAST_LIST) {
    $first = $n.Split(" ")[0].ToLower()
    $last = $n.Split(" ")[1].ToLower()
    $username = "$($first.Substring(0,1))$($last)".ToLower()
    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $first `
               -Surname $last `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_USERS,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
}
```

- click “enter” and watch the users being created


<br>
<p align="center">
<img src="https://imgur.com/h8Ynrzj.png"/>
</p>

<br>


- Now Check your users and computers in active directory
- Refresh
- You should see the “_Users”
- then right click and select “Find…”


<br>
<p align="center">
<img src="https://imgur.com/49xNUWI.png"/>
</p>

<br>


- then select “FInd now”

<br>
<p align="center">
<img src="https://imgur.com/lRvnTyF.png"/>
</p>

<br>

- you now see 1000 items(s) found →

  ##**Meaning 1000 users were created**

So we just basically created a mini, corporate network 

In this next section we will

- Edit the group policy to configure the number of invalid logon attempts before locking the account
- Attempt to remote desktop into a user account with incorrect details to lock the account
- We will unlock the account and do a password reset
- We will attempt to tog in again


















