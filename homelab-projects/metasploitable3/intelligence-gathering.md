# Intelligence Gathering

## Passive Reconnaissance (Skipped)

We will be skipping the Passive Reconnaissance on this phase since this is only a simulated lab environment and we cannot apply OSINT.

## Active Reconnaissance

We will use the following methods gather details on this endpoint.

### Port Scanning

**Command:**&#x20;

```bash
nmap -v2 -p - metasploitable3 -oA initial_scan 
```

**Result:**

```bash
Nmap scan report for metasploitable3 (172.16.16.131)
Host is up, received arp-response (0.00064s latency).
Scanned at 2025-03-10 09:50:23 PST for 113s
Not shown: 65524 filtered tcp ports (no-response)
PORT     STATE  SERVICE      REASON
21/tcp   open   ftp          syn-ack ttl 64
22/tcp   open   ssh          syn-ack ttl 64
80/tcp   open   http         syn-ack ttl 64
445/tcp  open   microsoft-ds syn-ack ttl 64
631/tcp  open   ipp          syn-ack ttl 64
3000/tcp closed ppp          reset ttl 64
3306/tcp open   mysql        syn-ack ttl 64
3500/tcp open   rtmp-port    syn-ack ttl 64
6697/tcp open   ircs-u       syn-ack ttl 64
8080/tcp open   http-proxy   syn-ack ttl 64
8181/tcp closed intermapper  reset ttl 64
MAC Address: 00:0C:29:E3:EF:36 (VMware)

```

### Service Scanning

**Command:** &#x20;

```bash
nmap -sV -sC -v -p - metasploitable3 -oA service_scan
```

**Result:**

```bash
──(kali㉿kali)-[~/Projects/Metasploitable3/IPT/scans]
└─$ nmap -sV -sC -v -p - metasploitable3 -oA service_scan
---SNIPPED FOR VERBOSITY--
Nmap scan report for metasploitable3 (172.16.16.131)
Host is up (0.00060s latency).
Not shown: 65524 filtered tcp ports (no-response)
PORT     STATE  SERVICE     VERSION
21/tcp   open   ftp         ProFTPD 1.3.5
22/tcp   open   ssh         OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 d0:7d:f2:06:65:ca:12:b2:25:62:99:66:81:d0:a7:ae (DSA)
|   2048 b1:a3:dc:b7:dd:b1:88:3c:75:11:64:aa:33:09:6d:df (RSA)
|   256 2f:96:42:73:ed:58:04:b9:a3:bc:c6:33:b0:73:a6:dd (ECDSA)
|_  256 9a:04:89:5b:fa:be:aa:1b:b9:49:0f:fe:eb:bf:b2:1b (ED25519)
80/tcp   open   http        Apache httpd 2.4.7
|_http-title: Index of /
| http-ls: Volume /
| SIZE  TIME              FILENAME
| -     2020-10-29 20:01  chat/
| -     2011-07-27 20:17  drupal/
| 1.7K  2020-10-29 20:01  payroll_app.php
| -     2013-04-08 12:06  phpmyadmin/
|_
|_http-server-header: Apache/2.4.7 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
445/tcp  open   netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
631/tcp  open   ipp         CUPS 1.7
|_http-title: Bad Request - CUPS v1.7.2
|_http-server-header: CUPS/1.7 IPP/2.1
3000/tcp closed ppp
3306/tcp open   mysql       MySQL (unauthorized)
3500/tcp open   http        WEBrick httpd 1.3.1 (Ruby 2.3.8 (2018-10-18))
|_http-title: Ruby on Rails: Welcome aboard
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: WEBrick/1.3.1 (Ruby/2.3.8/2018-10-18)
|_http-favicon: Unknown favicon MD5: D41D8CD98F00B204E9800998ECF8427E
6697/tcp open   irc         UnrealIRCd
8080/tcp open   http        Jetty 8.1.7.v20120910
|_http-title: Error 404 - Not Found
|_http-server-header: Jetty(8.1.7.v20120910)
|_http-favicon: Unknown favicon MD5: ED7D5C39C69262F4BA95418D4F909B10
8181/tcp closed intermapper
MAC Address: 00:0C:29:E3:EF:36 (VMware)
Service Info: Hosts: 127.0.0.1, UBUNTU, irc.TestIRC.net; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2025-03-10T01:42:47
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: ubuntu
|   NetBIOS computer name: UBUNTU\x00
|   Domain name: \x00
|   FQDN: ubuntu
|_  System time: 2025-03-10T01:42:50+00:00
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 1s, deviation: 2s, median: 0s

NSE: Script Post-scanning.
Initiating NSE at 09:43
Completed NSE at 09:43, 0.00s elapsed
Initiating NSE at 09:43
Completed NSE at 09:43, 0.00s elapsed
Initiating NSE at 09:43
Completed NSE at 09:43, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 159.84 seconds
           Raw packets sent: 131144 (5.770MB) | Rcvd: 96 (4.180KB)

```

### Banner Grabbing

#### Port 21 (Pro FTPD)

```bash
┌──(kali㉿kali)-[~/Projects/Metasploitable3/IPT/scans]
└─$ nc -nv 172.16.16.131 21         
(UNKNOWN) [172.16.16.131] 21 (ftp) open
220 ProFTPD 1.3.5 Server (ProFTPD Default Installation) [172.16.16.131]
```

**Port 22 (OpenSSH)**

```bash
┌──(kali㉿kali)-[~/Projects/Metasploitable3/IPT/scans]
└─$ nc -nv 172.16.16.131 22  
(UNKNOWN) [172.16.16.131] 22 (ssh) open
SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.13
```

**Port 6697 (Unreal IRCD)**                                                                              &#x20;

```bash
┌──(kali㉿kali)-[~/Projects/Metasploitable3/IPT/scans]
└─$ nc -nv 172.16.16.131 6697

(UNKNOWN) [172.16.16.131] 6697 (ircs-u) open
:irc.TestIRC.net NOTICE AUTH :*** Looking up your hostname...
:irc.TestIRC.net NOTICE AUTH :*** Couldn't resolve your hostname;
NICK Jj
USER Jj 0 * :Jj

:irc.TestIRC.net 001 Jj :Welcome to the TestIRC IRC Network Jj!Jj@172.16.16.240
:irc.TestIRC.net 002 Jj :Your host is irc.TestIRC.net, running version 
Unreal3.2.8.1
:irc.TestIRC.net 003 Jj :This server was created Thu Oct 29 2020 at 20:00:01 UTC
```

#### Port 445 (Samba)                                                                                                                                    &#x20;

```bash
┌──(kali㉿kali)-[~/Projects/Metasploitable3/IPT/scans]
└─$ smbclient -N -L \\\\172.16.16.131         

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        public          Disk      WWW
        IPC$            IPC       IPC Service (ubuntu server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 172.16.16.131 failed (Error NT_STATUS_IO_TIMEOUT)
Unable to connect with SMB1 -- no workgroup available
```

### **Directory/File Enumeration using GoBuster**

**Command:**

```bash
gobuster dir -u http://metasploitable3/ -w /usr/share/seclists/Discovery/Web-Content/common.txt -o metasploitable3_gobuster.txt
```

**Result:**

{% code fullWidth="false" %}
```bash
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 286]
/.htpasswd            (Status: 403) [Size: 291]
/.htaccess            (Status: 403) [Size: 291]
/cgi-bin/             (Status: 403) [Size: 290]
/chat                 (Status: 301) [Size: 316] [--> http://metasploitable3/chat/]
/drupal               (Status: 301) [Size: 318] [--> http://metasploitable3/drupal/]
/phpmyadmin           (Status: 301) [Size: 322] [--> http://metasploitable3/phpmyadmin/]
/server-status        (Status: 403) [Size: 295]
/uploads              (Status: 301) [Size: 319] [--> http://metasploitable3/uploads/]
Progress: 4744 / 4745 (99.98%)
===============================================================
Finished
===============================================================
```
{% endcode %}

### Web Enumeration

#### **Whatweb**

**Command:**

```bash
whatweb --no-errors http://metasploitable3
```

**Result:**

```bash
┌──(kali㉿kali)-[~/Projects/Metasploitable3/IPT/scans]
└─$ whatweb --no-errors http://metasploitable3
http://metasploitable3 [200 OK] Apache[2.4.7], Country[RESERVED][ZZ], 
HTTPServer[Ubuntu Linux][Apache/2.4.7 (Ubuntu)], IP[172.16.16.131], 
Index-Of, Title[Index of /]                                       
```

