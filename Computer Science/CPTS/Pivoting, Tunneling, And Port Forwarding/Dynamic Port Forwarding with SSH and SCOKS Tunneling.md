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