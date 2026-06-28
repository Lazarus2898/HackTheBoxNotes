### Starting Information
```bash
Job is a Medium difficulty Windows box. It runs an SMTP server and its website accepts LibreOffice-compatible documents, providing a vector to deliver a document with embedded macros that leads to remote code execution as user `jack.black`. `jack.black` is a member of the `DEVELOPERS` group, which has write access to `C:\inetpub\wwwroot` (the IIS web root), allowing files to be placed in the webroot and achieve code execution as the IIS AppPool service account. The IIS AppPool account has the SeImpersonate privilege, creating conditions that allow token-impersonation techniques to be used to escalate privileges to Administrator.
```


| Difficultly | Victim IP     | Host IP      | Operating System |
| ----------- | ------------- | ------------ | ---------------- |
| Medium      | 10.129.234.73 | 10.10.16.150 | Windows          |

### Initial Enumeration
`nmap -sC -sV -v 10.129.234.73`
```bash
PORT     STATE SERVICE       VERSION
25/tcp   open  smtp          hMailServer smtpd
| smtp-commands: JOB, SIZE 20480000, AUTH LOGIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-title: Job.local
|_http-server-header: Microsoft-IIS/10.0
|_http-favicon: Unknown favicon MD5: 556F31ACD686989B1AFCF382C05846AA
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: JOB
|   NetBIOS_Domain_Name: JOB
|   NetBIOS_Computer_Name: JOB
|   DNS_Domain_Name: job
|   DNS_Computer_Name: job
|   Product_Version: 10.0.20348
|_  System_Time: 2026-06-28T17:25:49+00:00
| ssl-cert: Subject: commonName=job
| Issuer: commonName=job
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-06-27T17:20:20
| Not valid after:  2026-12-27T17:20:20
| MD5:     c693 2971 4292 1077 861a da4a 53fa f022
| SHA-1:   c3c6 0bf4 903f 7ff0 af1d 8955 49e7 591c d625 da4a
|_SHA-256: 20fb 27d7 1818 fc27 4838 fe00 25e3 2182 9f2b 4055 60c3 cdd7 a27e 43c1 77aa 8171
|_ssl-date: 2026-06-28T17:26:28+00:00; +1s from scanner time.
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: Host: JOB; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2026-06-28T17:25:53
|_  start_date: N/A
|_clock-skew: mean: 1s, deviation: 0s, median: 1s
```