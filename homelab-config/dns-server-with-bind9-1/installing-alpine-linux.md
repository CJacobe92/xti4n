---
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

# Installing Alpine Linux

## Setting Up Alpine Linux

1. Download CentOS from the official site.
2. Configure your favorite hypervisor with the following requirements above.
3.  Use the username `root` and hit enter

    <div align="left"><figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure></div>
4. Type `setup-alpine` and set the following configuration
   * Select keyboard layout\[none]: \<Enter>
   * Enter system hostname: \<Your server FQDN>
   * Which one do you want to initialize: \<Enter>
   * Ip address for \<interface>: \<Enter your servers preferred IP address>
   * Netmask: 255.255.255.0
   * Gateway: \<Your gateway>
   * Do you want to do any manual network configuration? n
   * DNS Domain Name: \<Enter>
   * DNS nameserver(s): 1.1.1.1 (We will change it later)
   * New password: \<Setup your root password>
   * Retype password: \<Retype your root password>
   * Which timezone you are in: \<Your timezone>
   * HTTP/Proxy URL? None
   * Which NTP client to run: Chrony
   * Enter the mirror number or URL: f
   * Setup a user: \<Your preferred user>
   * Full name for user: \<Your full name>
   * New password: \<Setup your user password>
   * Retype password: \<Retype your user password>
   * Enter ssh key or URL for \<user>: \<Enter>
   * Which ssh server: openssh
   * Which disk you would like to use: sda
   * How you would like to use it: sys
   * Warning: Erase above disk(s) and continue?: y

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

The installation is now complete. You can reboot or poweroff the VM
