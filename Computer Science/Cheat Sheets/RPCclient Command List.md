### Base Log in
When using `RPC` commands, if `enum4linux-ng` fails, using RPC to continue enumeration or doing enumeration by hand is possible.
```bash
# For anonymous log on
rpcclient -U '' <IP>

# User log on
rpcclient -U 'guest' -p '' <IP>
```
### Base Enumeration
```bash
# List the users and the rid numbers
enumdomusers

# List the groups and their rid numbers
enumdomgroups
```

### Detailed Enumeration
While in the RPC client one can run the following commands
```bash
# Provides system ID/Domain
lsaquery

# Provides information (i.e username)
lookupsids <sid number>-<500,1000s, or 1100s>

# Provides information regarding the user and their rid number 
queryuser <rid number>

# Provides information regarding the group and its rid number
querygroups <rid number>
```