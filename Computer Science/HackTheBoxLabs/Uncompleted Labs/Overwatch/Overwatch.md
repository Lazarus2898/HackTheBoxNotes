Starting off with an `nmap` scan!
`nmap -v -sC -Pn -sV -oA nmap/overwatch/ 10.129.244.81`
```bash
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-02-18 04:24:49Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: overwatch.htb, Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: overwatch.htb, Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=S200401.overwatch.htb
| Issuer: commonName=S200401.overwatch.htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-12-07T15:16:06
| Not valid after:  2026-06-08T15:16:06
| MD5:     0da8 f9a5 d788 e363 07b1 5f70 6524 ffcb
| SHA-1:   3287 c62d 4408 7fbb 4038 00b3 32fa da67 fb22 14bc
|_SHA-256: b8ca 73a4 d338 1c57 3558 eec9 d8d1 9381 5b2d e30e 7945 ff69 0565 8935 84da f28a
|_ssl-date: 2026-02-18T04:25:34+00:00; +2s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: OVERWATCH
|   NetBIOS_Domain_Name: OVERWATCH
|   NetBIOS_Computer_Name: S200401
|   DNS_Domain_Name: overwatch.htb
|   DNS_Computer_Name: S200401.overwatch.htb
|   DNS_Tree_Name: overwatch.htb
|   Product_Version: 10.0.20348
|_  System_Time: 2026-02-18T04:24:54+00:00
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: Host: S200401; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
|_clock-skew: mean: 1s, deviation: 0s, median: 1s
| smb2-time: 
|   date: 2026-02-18T04:24:55
|_  start_date: N/A

```

Take aways:
1. SMB anonymous sign on
2. Domain: overwatch.htb

We can add that to the /etc/host file!

### Enumeration
Running enum4linux-ng.
1. Anonymous SMB
2. Anonymous RPC

```bash
┌──(lazarus㉿Xerek)-[~]
└─$ rpcclient -U '' 10.129.244.81
Password for [WORKGROUP\]:
rpcclient $> lsaquery
Domain Name: OVERWATCH
Domain Sid: S-1-5-21-2797066498-1365161904-233915892
```
With that running `netexec` one can get all the sids!
```bash       
┌──(lazarus㉿Xerek)-[~]
└─$ netexec smb 10.129.244.81 -u guest -p '' --rid-brute
SMB         10.129.244.81   445    S200401          [*] Windows Server 2022 Build 20348 x64 (name:S200401) (domain:overwatch.htb) (signing:True) (SMBv1:False)
SMB         10.129.244.81   445    S200401          [+] overwatch.htb\guest: 
SMB         10.129.244.81   445    S200401          498: OVERWATCH\Enterprise Read-only Domain Controllers (SidTypeGroup)
SMB         10.129.244.81   445    S200401          500: OVERWATCH\Administrator (SidTypeUser)

```

And the list of users are available!