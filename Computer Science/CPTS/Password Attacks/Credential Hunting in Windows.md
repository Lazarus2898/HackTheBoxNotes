# The Hunting
### LaZagne
[GitHub Repo](https://github.com/AlessandroZ/LaZagne.git)

| Module   | Description                                                                                       |
| -------- | ------------------------------------------------------------------------------------------------- |
| browsers | Extracts passwords from various browsers including Chromium, Firefox, Microsoft Edge, and Opera   |
| chats    | Extracts passwords from various chat applications including Skype                                 |
| mails    | Searches through mailboxes for passwords including Outlook and Thunderbird                        |
| memory   | Dumps passwords from memory, targeting KeePass and LSASS                                          |
| sysadmin | Extracts passwords from the configuration files of various sysadmin tools like OpenVPN and WinSCP |
| windows  | Extracts Windows-specific credentials targeting LSA secrets, Credential Manager, and more         |
| wifi     | Dumps WiFi credentials                                                                            |
Using a file transfer method to move to the victim unit and run with `start LaZagne.exe all`
### Other Methods
Using the findstr command.
`C:\> findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml`
