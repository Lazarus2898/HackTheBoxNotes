# The Context of Port Forwarding
Giving an nmap scan lets say `SSH p22` and `mySQL p3306` is open and closed.
```bash
ssh -L 1234:localhost:3306 ubuntu@10.129.202.64
```
The `-L` command allows for the port forwarding on the SSH port.
One can also confirm port forwarding with.
```bash
netstat -antp | grep 1234
```

You can even forward multiple ports by doing the following.
```bash
ssh -L 1234:localhost:3306 -L 8080:localhost:80 ubuntu@10.129.202.64
```

### Setting up a Pivot
Looking into the `ens` range, these are the establishments of other connections to victim machines.

### Enabling Dynamic Port Forwarding with SSH
```bash
ssh -D 9050 ubuntu@10.129.202.64
```

### Using/Checking proxychains.conf
```bash
tail -4 /etc/proxpchains.conf

# Other proxies could be SOCKS4, SOCKS5, TOR, HTTP, HTTPS
sock4 127.0.0.1 9050
```
Now when you start Nmap with proxychains using the below command, it will route all the packets of Nmap to the local port 9050, where our SSH client is listening, which will forward all the packets over SSH to the 172.16.5.0/23 network.
```bash
proxychains nmap -v -sn 172.16.5.1-200
```

### Using Metasploit with Proxychains or RDP
`proxychains msfconsole`
Using examples such as `rdp_scanner`

`proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123`
