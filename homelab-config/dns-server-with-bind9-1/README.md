---
icon: flask-gear
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

# DNS Server with Bind9

In an effort to simulate the internet infrastructure, I've built a self-contained environment that integrates both authoritative and recursive DNS servers. This virtual production system allows me to emulate a real-world network setup while exploring ethical hacking, advanced network security, and DNS architecture—all without relying on live production servers. This setup not only deepens my understanding of core internet protocols but also offers a safe space for experimentation and learning.

## <mark style="color:orange;">Requirements</mark>

* 2x Alpine Linux server with 512mb ram, 10GB HDD and 1 vCPU with your preferred **FQDN** (Fully Qualified Domain Name) e.g. **ns1.aidentrix.lan** and **dns.aidentrix.lan**

<figure><img src="../../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>
