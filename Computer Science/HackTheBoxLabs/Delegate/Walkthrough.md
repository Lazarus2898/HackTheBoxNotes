# Enumeration
The IP address of this machine is: `10.129.234.69`

```bash
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-06-17 01:03:55Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: delegate.vl, Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: delegate.vl, Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2026-06-17T01:04:44+00:00; -13s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: DELEGATE
|   NetBIOS_Domain_Name: DELEGATE
|   NetBIOS_Computer_Name: DC1
|   DNS_Domain_Name: delegate.vl
|   DNS_Computer_Name: DC1.delegate.vl
|   DNS_Tree_Name: delegate.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2026-06-17T01:04:05+00:00
| ssl-cert: Subject: commonName=DC1.delegate.vl
| Issuer: commonName=DC1.delegate.vl
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-06-16T01:01:57
| Not valid after:  2026-12-16T01:01:57
| MD5:     edbf f143 3606 cd77 f532 d2e6 6b3b cd5b
| SHA-1:   eb02 5c88 bcaf 6477 9264 979e a5ec d425 1eae ba13
|_SHA-256: 876d f34e 76b9 35df 1382 6f16 22dd ac6a c938 3fa7 3bef 2dba 7344 ae2d cbf5 927a
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: Host: DC1; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
|_clock-skew: mean: -13s, deviation: 0s, median: -13s
| smb2-time: 
|   date: 2026-06-17T01:04:07
|_  start_date: N/A
```

### Step 1
Form this, we can see that the domain is `delegate.vl`
