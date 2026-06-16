# The Enumeration
The IP address = 10.129.15.197
```bash
└─$ nmap 10.129.15.197 -sC -sV   
Starting Nmap 7.98 ( https://nmap.org ) at 2026-06-15 20:42 -0400
Nmap scan report for 10.129.15.197
Host is up (0.046s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    HttpFileServer httpd 2.3
|_http-server-header: HFS 2.3
|_http-title: HFS /
```
From this scan port 80 is open offering a website.
Looking at the version, `httpfileserver 2.3`, there is a CVE just on this for a logon.

# Exploitation
Going to `msfconsole -q`, search for `CVE-2014-7226` comes up with an exploit.
Filling out the correct information for the `RHOST, LHOST, RPORT`. And running execute, will give you access to the user of the machine.

Now going back to the meterpreter shell, running the command `sysinfo` will tell us that the machine is a `Windows 2012 R2 Server`. Looking into the correct exploit for the machine, Completing the required information will allow for access to the root account. 

Now when running the meterpreter, using the `background` command will allow you to store the session is the background, find the correct exploit, then use the session with access to gain privilege escalation to the machine.