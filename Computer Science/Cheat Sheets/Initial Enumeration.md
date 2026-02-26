# Scanning
### Nmap Scanning
1. Creating a directory for initial scan output.
```bash
mkdir <dirName>
nmap -sC -sV -oA nmap/<dirName>
```
2. Scanning with nmap with options

| Option | Description      |
| ------ | ---------------- |
| -sC    | Script-Scanning  |
| -sV    | Version-Scanning |

# Machine Enumeration
## Initial Commands
### Enum4linux-ng
```shell
# Initial Scan
enum4linux-ng <IP>

# Looking for usernames
enum4linux-ng <IP> | grep username
```

### Netexec
[Netexec Documentation](https://www.netexec.wiki/)
Another command you can use is `netexec`, with this one can...
```shell
# Check the password policy
netexec smb <IP> -u 'user' -p 'pass' --pass-pol

# Brute force SIDs
netexec smb <IP> -u 'user' -p 'pass' --rid-force
```
## Port Commands
### SMB
If after scanning the target machine has smb open on traditionally `137, 138, 139` and SMB being on port `445`.
If the one can log on anonymously you can run
```Bash 
# Initial Anon log on.
smbclient -L //<IP> -N

# Looking at direct shares
smbclient //<IP>/share
```

After that one can run commands such as `dir` or `type <IP>`


### RPC [[RPCclient Command List]]

If there is an RPC port on the machine you can run the following commands. Sometimes logging in as guest or as a user can render different results.
```bash
# For anonymous log on
rpcclient -U '' <IP>

# User log on
rpcclient -U 'guest' -p '' <IP>
```

Once logged in you can use he following commands to enumerate the machine
```bash
# Enumerate the users of this machine
rpcclient $> enumdomusers

# Enumerate the privileges of this machine
rpcclient$> enumprivs

# Enumerating the SID's of the machine
rpcclient $> lsaquery

# After getting the SID's you can do lookups by appending group numbers to the end of the string the output is below.
rpcclient $> lookupsids S-1-5-21-917908876-1423158569-3159038727-501
S-1-5-21-917908876-1423158569-3159038727-501 CICADA\Guest (1)

# Groups range for 500 ish to about the 1000-1100 range for user ids
rpcclient $> lookupsids S-1-5-21-917908876-1423158569-3159038727-1106
S-1-5-21-917908876-1423158569-3159038727-1106 CICADA\michael.wrightson (1)
```

