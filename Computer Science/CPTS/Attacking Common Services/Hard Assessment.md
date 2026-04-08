IP: `10.129.203.10`

# Enumeration
`nmap -sV -v 10.129.203.10` then enumerated the ports found with `-sC`
```bash

PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
445/tcp  open  microsoft-ds?
1433/tcp open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-info: 
|   10.129.203.10:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ms-sql-ntlm-info: 
|   10.129.203.10:1433: 
|     Target_Name: WIN-HARD
|     NetBIOS_Domain_Name: WIN-HARD
|     NetBIOS_Computer_Name: WIN-HARD
|     DNS_Domain_Name: WIN-HARD
|     DNS_Computer_Name: WIN-HARD
|_    Product_Version: 10.0.17763
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Issuer: commonName=SSL_Self_Signed_Fallback
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-04-08T01:33:06
| Not valid after:  2056-04-08T01:33:06
| MD5:     7858 4350 a942 7ab8 32b7 8c80 a7cd 4bfc
| SHA-1:   b17a aef0 248c a239 8dad 6a9f a553 8f07 de3d f12e
|_SHA-256: 0658 a24c d618 b17c 4067 14f5 d174 e772 6391 6c11 57a9 e505 eee9 7071 5d00 2f74
|_ssl-date: 2026-04-08T01:45:31+00:00; +1s from scanner time.
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WIN-HARD
|   NetBIOS_Domain_Name: WIN-HARD
|   NetBIOS_Computer_Name: WIN-HARD
|   DNS_Domain_Name: WIN-HARD
|   DNS_Computer_Name: WIN-HARD
|   Product_Version: 10.0.17763
|_  System_Time: 2026-04-08T01:44:51+00:00
|_ssl-date: 2026-04-08T01:45:31+00:00; +1s from scanner time.
| ssl-cert: Subject: commonName=WIN-HARD
| Issuer: commonName=WIN-HARD
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-04-07T01:32:54
| Not valid after:  2026-10-07T01:32:54
| MD5:     b90c 610a b281 2bc9 2cd8 3e18 aa87 cc4c
| SHA-1:   37fd 90cb 669f 3861 68fb fba1 5e15 6a93 b1c9 bafa
|_SHA-256: 5dd3 d5bd ea3f 7942 3bbe 30c5 b9f5 7f5b 0836 d391 123f 6757 5f65 87f4 6aa0 3207
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2026-04-08T01:44:51
|_  start_date: N/A

NSE: Script Post-scanning.
Initiating NSE at 21:45
Completed NSE at 21:45, 0.00s elapsed
Initiating NSE at 21:45
Completed NSE at 21:45, 0.00s elapsed
Initiating NSE at 21:45
Completed NSE at 21:45, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 53.06 seconds
           Raw packets sent: 8 (328B) | Rcvd: 5 (204B)

```