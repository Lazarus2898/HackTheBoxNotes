# Pluggable Authentication Modules (PAM)
* `pam_unix.so`
* `pam_unix2.so`
Located in `/usr/lib/x86_64-linux-gnu/security/`
The primary files it writes to `/etc/passwd` and `/etc/shadow`

### Password File
```bash
htb-student:x:1000:1000:,,,:/home/htb-student:/bin/bash
# User: htb-student
# Pass: x
# User ID: 1000
# Group ID: 1000
# GECOs: ,,,
# Home Directory: /home/htb-student
# Default shell: /bin/bash
```

### Shadow File
```bash
htb-student:$y$j9T$3QSBB6CbHEu...SNIP...f8Ms:18955:0:99999:7:::
# Username	htb-student
# Password	$y$j9T$3QSBB6CbHEu...SNIP...f8Ms
# Last change	18955
# Min age	0
# Max age	99999
# Warning period	7
# Inactivity period	-
# Expiration date	-
# Reserved field	-
```

### Hashes
| ID     | Cryptographic Hash Algorithm                                          |
| ------ | --------------------------------------------------------------------- |
| `1`    | [MD5](https://en.wikipedia.org/wiki/MD5)                              |
| `2a`   | [Blowfish](https://en.wikipedia.org/wiki/Blowfish_\(cipher\))         |
| `5`    | [SHA-256](https://en.wikipedia.org/wiki/SHA-2)                        |
| `6`    | [SHA-512](https://en.wikipedia.org/wiki/SHA-2)                        |
| `sha1` | [SHA1crypt](https://en.wikipedia.org/wiki/SHA-1)                      |
| `y`    | [Yescrypt](https://github.com/openwall/yescrypt)                      |
| `gy`   | [Gost-yescrypt](https://www.openwall.com/lists/yescrypt/2019/06/30/1) |
| `7`    | [Scrypt](https://en.wikipedia.org/wiki/Scrypt)                        |

### Previously Used Passwords
`/etc/security/opasswd`
Type: `MD5` Hash

```bash
Lazarus2858@htb[/htb]$ sudo cp /etc/passwd /tmp/passwd.bak 
Lazarus2858@htb[/htb]$ sudo cp /etc/shadow /tmp/shadow.bak 
Lazarus2858@htb[/htb]$ unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes
Lazarus2858@htb[/htb]$ hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked
```

### John and the Passwords
Given an zipped file of passwd and shadow.
```bash
unshadow passwd shadow| tee unshadowed
john --single unshadowed

Using default input encoding: UTF-8
Loaded 2 password hashes with 2 different salts (sha512crypt, crypt(3) $6$ [SHA512 128/128 SSE2 2x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 6 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
Martin1          (martin)     
Almost done: Processing the remaining buffered candidate passwords, if any.
1g 0:00:00:02 DONE (2026-03-31 20:49) 0.3773g/s 2879p/s 2879c/s 2879C/s ssarah1900..saragadaysarah1900
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

Martins Hash cracked!
```

```bash
hashcat -a 0 -m 1800 $6$EBOM5vJAV1TPvrdP$LqsLyYkoGzAGt4ihyvfhvBrrGpVjV976B3dEubi9i95P5cDx1U6BrE9G020PWuaeI6JSNaIDIbn43uskRDG0U/ /user/shard/wordlists/rockyou.txt
```