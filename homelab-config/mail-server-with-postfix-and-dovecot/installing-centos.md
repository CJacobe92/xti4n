# Installing CentOS

## Configuring the CentOS Installation

1.  Select your language and click continue



    <div align="left"><figure><img src="../../.gitbook/assets/image (222).png" alt="" width="563"><figcaption></figcaption></figure></div>
2.  Select **System > Installation Destination** and select the disk to install the server and click **Done**



    <div align="left"><figure><img src="../../.gitbook/assets/image (225).png" alt="" width="563"><figcaption></figcaption></figure></div>



    <div align="left"><figure><img src="../../.gitbook/assets/image (223).png" alt="" width="563"><figcaption></figcaption></figure></div>
3.  Select **User Account > User Creation** and create your user make sure that it has administration rights. We will keep the root account disabled.



    <div align="left"><figure><img src="../../.gitbook/assets/image (226).png" alt="" width="563"><figcaption></figcaption></figure></div>



    <div align="left"><figure><img src="../../.gitbook/assets/image (224).png" alt="" width="563"><figcaption></figcaption></figure></div>
4.  Select **Software** > **Software Selection,** using this option we will make some changes on the server



    <div align="left"><figure><img src="../../.gitbook/assets/image (227).png" alt="" width="563"><figcaption></figcaption></figure></div>
5.  Follow the selection below and click **Done**



    <div align="left"><figure><img src="../../.gitbook/assets/image (228).png" alt="" width="563"><figcaption></figcaption></figure></div>
6.  Select **Begin Installation** and wait for the installation to finish.



    <div align="left"><figure><img src="../../.gitbook/assets/image (229).png" alt="" width="563"><figcaption></figcaption></figure></div>

## Post Installation Setup

## Setting up the hostname

1.  Configure the hostname using the command below:

    `sudo hostnamectl set-hostname mail.aidentrix.lan`&#x20;
2. &#x20;Reboot the server `sudo reboot -f`&#x20;

### Network configuration using nmcli

1.  Check your network interface using `nmcli device show`&#x20;



    <figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>
2.  Set your IPv4 address

    `nmcli connection modify ens33 ipv4.addresses 172.16.16.20/24`&#x20;
3.  Set your gateway

    `nmcli connection modify ens33 ipv4.gateway 172.16.16.2`&#x20;
4.  Set your DNS we will use the IPv4 of our **dns.aidentrix 172.16.16.16**

    `nmcli connection modify ens33 ipv4.dns 172.16.16.16`&#x20;
5.  Set the method to manual

    `nmcli connection modify ens33 ipv4.method manual`&#x20;
6.  Enable the configuration

    `nmcli connection up ens33`&#x20;

#### Summary of the commands used

```bash
sudo nmcli device show
sudo nmcli connection modify ens33 ipv4.addresses 172.16.16.20/24 
sudo nmcli connection modify ens33 ipv4.gateway 172.16.16.2
sudo nmcli connection modify ens33 ipv4.dns "172.16.16.16"
sudo nmcli connection modify ens33 ipv4.method manual
sudo nmcli connection up ens33
```

## Configuring the firewall

1. Check if the firewall is running

```bash
[atadmin@mail ~]$ sudo firewall-cmd --state
running
```

2. Checking the default zones

```bash
[atadmin@mail ~]$ sudo firewall-cmd --get-default-zone
public
```

3. Checking the zones controlling the traffic

```bash
[atadmin@mail ~]$ sudo firewall-cmd --get-active-zones
public (default)
  interfaces: ens33
```

4. Checking the rules associated to the zone

```bash
[atadmin@mail ~]$ sudo firewall-cmd --zone=public --list-all
public (default, active)
  target: default
  ingress-priority: 0
  egress-priority: 0
  icmp-block-inversion: no
  interfaces: ens33
  sources:
  services: cockpit dhcpv6-client ssh
  ports:
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

4. Add common ports such as **443**, **80** and **53.**

```bash
[atadmin@mail ~]$ sudo firewall-cmd --zone=public --permanent --add-port=443/tcp
success
[atadmin@mail ~]$ sudo firewall-cmd --zone=public --permanent --add-port=80/tcp
success
[atadmin@mail ~]$ sudo firewall-cmd --zone=public --permanent --add-port=53/tcp
success
[atadmin@mail ~]$ sudo firewall-cmd --zone=public --permanent --add-port=53/udp
success
[atadmin@mail ~]$ sudo firewall-cmd --zone=public --list-ports
53/tcp 80/tcp 443/tcp 53/udp
```

5. Restart the firewall service using `sudo firewall-cmd --reload`&#x20;

#### Summary of the commands used

```bash
sudo firewall-cmd --state
sudo firewall-cmd --get-default-zone
sudo firewall-cmd --zone=public --list-all
sudo firewall-cmd --zone=public --permanent --add-port=443/tcp
sudo firewall-cmd --zone=public --permanent --add-port=80/tcp
sudo firewall-cmd --zone=public --permanent --add-port=53/tcp
sudo firewall-cmd --zone=public --permanent --add-port=53/udp
sudo firewall-cmd --zone=public --list-ports
```

#### Perform an update using the command `sudo dnf update`
