#cheatsheets
# User-Pass Spraying
### Netexec
```bash
# Remote log on 
netexec winrm <IP> -u user.txt -p pass.txt
```

### Hydra
```bash
# SSH log on
hydra -L user.txt -P pass.txt ssh://<IP>

# RDP log on
hydra -L user.txt -P pass.txt rdp//<IP>
```


# Hash Cracking 
[CrackStation](https://crackstation.net/)
