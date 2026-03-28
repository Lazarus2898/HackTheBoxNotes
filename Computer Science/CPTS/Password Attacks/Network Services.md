
# Password Attacks
Is this section numerous attacks take place, such as:
* Password spraying
* Credential stuffing
* Default Credentials
### Netexec
`netexec smb <IP/24"> -u userlist -p 'password'`

### Hydra
`hydra -C userlist ssh://<IP>`


## Default Creds List
```bash
sudo apt install pypy3-venv

pip3 install defaultcreds-cheat-sheet
creds search linksys
```

```bash
# MYSQL default creds
superdba:superdba  
superdba:password  
superdba:admin  
superdba:P@ssw0rd  
superdba:123456
```