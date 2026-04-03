# Window Shares
### Tools
`Snaffler (Domain joined machine)`
* `Snaffler.exe -s`
`PowerHuntShares`
* `Invoke-HuntSMBShares -Threads 100 -OutputDirectory c:\Users\Public`
`Manspider`
* `docker run --rm -v ./manspider:/root/.manspider blacklanternsecurity/manspider 10.129.234.121 -c 'passw' -u 'mendres' -p 'Inlanefreight2025!'`
*  Should be run in docker to avoid dependency issues

While the above should be run inside the windows machine, netexec has the ability to log in remotely and find any information regarding the shares.
`Netexec`
* `nxc smb 10.129.234.121 -u mendres -p 'Inlanefreight2025!' --spider IT --content --pattern "passw"`

