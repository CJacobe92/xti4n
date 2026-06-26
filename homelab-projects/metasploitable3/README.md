---
icon: user-hoodie
---

# Metasploitable3

## What is Metasploitable3?&#x20;

Metasploitable3 is a virtual machine (VM) created by Rapid7, designed to be a vulnerable target for testing and practicing penetration testing techniques. It is built from the ground up with a wide range of security vulnerabilities, making it an excellent tool for learning and honing your skills in exploiting various types of vulnerabilities.



On this write up we will follow **PTES (Penetration Testing Execution Standard)**.

## **Phase of PTES (Penetration Testing and Execution Standard)**

* **Pre-engagement Interactions:** Define scope, objectives, and rules of engagement. (Skipped for this write up).
* **Intelligence Gathering:** Collect data on the target (e.g., reconnaissance).
* **Threat Modeling:** Identify vulnerabilities and prioritize potential threats (Skipped for this write up).
* **Vulnerability Analysis:** Perform scans and manual analysis for weaknesses.
* **Exploitation:** Exploit vulnerabilities to gain unauthorized access.
* **Post-Exploitation:** Evaluate the impact, extract data, and establish persistence (if needed).
* **Reporting:** Document findings, vulnerabilities, exploits, and mitigation steps.

## Setting up the target machine

Our target is our locally hosted **Metasploitable3** with an ipv4 address of **172.16.16.131.** For ease of access, we will add an entry on our **/etc/hosts** file using the command below:

`echo "metasploitable3 172.16.16.131" | sudo tee -a /etc/hosts` <br>

Ping the metasploitable3 if your entry is correct, it should return a response with the ipv4 address of your **Metasploitable3** machine.

**Endpoint:** https://metasploitable3

```bash
┌──(kali㉿kali)-[~/Projects/Metasploitable3/IPT/scans]
└─$ ping metasploitable3 -c 4
PING metasploitable3 (172.16.16.131) 56(84) bytes of data.
64 bytes from metasploitable3 (172.16.16.131): icmp_seq=1 ttl=64 time=0.504 ms
64 bytes from metasploitable3 (172.16.16.131): icmp_seq=2 ttl=64 time=0.576 ms
64 bytes from metasploitable3 (172.16.16.131): icmp_seq=3 ttl=64 time=0.445 ms
64 bytes from metasploitable3 (172.16.16.131): icmp_seq=4 ttl=64 time=1.16 ms

--- metasploitable3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3084ms
rtt min/avg/max/mdev = 0.445/0.672/1.164/0.287 ms
```
