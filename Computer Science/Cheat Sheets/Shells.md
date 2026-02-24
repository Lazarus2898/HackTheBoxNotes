# Basic Commands
`pwd` print working directiory
`whoami` prints the name of the user
`sudo -i` to check the sudo permissions of the fle

# Adding TTY for better shells
```bash
python -c 'import pty; pty.spawn("/bin/sh")' 
```

### Other Ways to Launch Shells
```shell
# Bin/sh -i
/bin/sh -i
sh: no job control in this shell
sh-4.2$

# Perl
perl —e 'exec "/bin/sh";'

# Ruby
ruby: exec "/bin/sh"

# Lua
lua: os.execute('/bin/sh')

# AWK
awk 'BEGIN {system("/bin/sh")}'

# Find
find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;

# Using Exec
find . -exec /bin/sh \; -quit

# Vim to shell and escape
vim -c ':!/bin/sh'

vim
:set shell=/bin/sh
:shell
```

# Downloadable Web Shells
In Web shells [[9. Web Shells]]
[Laudanum](https://github.com/jbarcia/Web-Shells/tree/master/laudanum)

