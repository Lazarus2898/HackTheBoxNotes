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
enum4linux-ng <IP>
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