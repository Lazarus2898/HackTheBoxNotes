Local Security Authority (LSA) is a protected subsystem that authenticates the users, manages local php logins and oversees all aspects of the local security.
## Logon process
* User logins
	* Local Security Authority Subsystem Service (LSASS)
		* Security Account Manager (Sam)



| Authentication Packages | Descriptions                                                                                                                                                                                                                                                   |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Lsasrv.dll              | The LSA Server service both enforces security policies and acts as the security package manager for the LSA. The LSA contains the Negotiate function, which selects either the NTLM or Kerberos protocol after determining which protocol is to be successful. |
| Msv1_0.dll              | Authentication package for local machine logons that don't require custom authentication.                                                                                                                                                                      |
| Samsrv.dll              | The Security Accounts Manager (SAM) stores local security accounts, enforces locally stored policies, and supports APIs.                                                                                                                                       |
| Kerberos.dll            | Security package loaded by the LSA for Kerberos-based authentication on a machine.                                                                                                                                                                             |
| Netlogon.dll            | Network-based logon service.                                                                                                                                                                                                                                   |
| Ntdsa.dll               | Directory System Agent (DSA) that manages the Active Directory database (ntds.dit), processes LDAP queries, and handles replication between domain controllers. Only loaded on Domain Controllers.                                                             |

### LSASS
Local Security Authority Subsystem Service
Located in: `%SystemRoot%\System32\Lsass.exe`
Responsibility is forwarding security audit logs to the Event Log.

### SAM
**None-Domain**
Security Account Manager
Located in: `%SystemRoot%\system32\config\SAM`
Mounted in: `HKLM\SAM`
Privileges: `System`
Responsibility is storing account creds of the machine
Hashes: `LM or NTLM`

**Domain**
DC must validate the credentials from the active directory database `(ntds.dit)` 
Stored in: `%SystemRoot%\ntds.dit`

### NTDS
Each Domain Controller hosts a file called NTDS.dit
Synced over all DC's in the forest
Read Only Domains
*  User Accounts
* Group Accounts
* Computer Accounts
* Group Policy objects