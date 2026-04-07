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