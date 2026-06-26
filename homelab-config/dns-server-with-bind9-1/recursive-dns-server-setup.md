# Recursive DNS Server Setup

ashIn the vast landscape of the Domain Name System (DNS), recursive DNS servers play a critical role as intermediaries, tirelessly working behind the scenes to resolve queries for end-users. Unlike authoritative DNS servers, which hold direct records for domains, recursive servers navigate through the hierarchy of DNS servers to fetch the required information. In this guide, we will delve into the configuration of a recursive DNS server, empowering our home lab network with efficient and streamlined name resolution capabilities.

## Install Bind9 on Alpine

1.  On the alpine terminal perform the following command to update the repositories&#x20;

    `doas apk update`&#x20;



    <figure><img src="../../.gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>
2.  Install bind9 using `doas apk add bind`&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (220).png" alt="" width="377"><figcaption></figcaption></figure></div>
3.  Confirm that the bind9 is properly by issuing `named -v`&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (221).png" alt=""><figcaption></figcaption></figure></div>

## Configuring named.conf

1. Create your **named.conf** file using `doas touch /etc/bind/named.conf`&#x20;
2.  Reference the **named.conf.authoritative** on your **named.conf** file

    `doas echo 'include "/etc/bind/named.conf.recursive";' | doas tee /etc/bind/named.conf`&#x20;
3. Checked the named.conf if the configuration you added is correct by typing `doas named-checkconf /etc/bind/named.conf`. If there are no errors means your config if correct and has no typographical errors.

## Configuring named.conf.recursive

1.  Open your **named.conf.authoritative** for editing using the nano editor

    `doas nano /etc/bind/named.conf.recursive`
2. Use the following configuration below:

```bash
options {
        directory "/var/bind";

        allow-recursion { 127.0.0.1/32; any; };
        recursion yes;

        forwarders {
                172.16.16.16;
                1.1.1.1;
                8.8.8.8;
                8.8.4.4;
        };


        listen-on { 127.0.0.1; any; };
        listen-on-v6 { none; };

        pid-file "/var/run/named/named.pid";

        allow-transfer { none; };
        dnssec-validation no;
        max-cache-size 256M;
        max-ncache-ttl 3600;

};

zone "." IN {
        type hint;
        file "named.ca";
};

zone "localhost" IN {
        type master;
        file "pri/localhost.zone";
        allow-update { none; };
        notify no;
};

zone "127.in-addr.arpa" IN {
        type master;
        file "pri/127.zone";
        allow-update { none; };
        notify no;
};

zone "aidentrix" IN {
        type forward;
        forward only;
        forwarders { 172.16.16.10; };
};

zone "aidentrix.lan" IN {
        type forward;
        forward only;
        forwarders { 172.16.16.10; };
};

zone "16.16.172.in-addr.arpa" IN {
        type forward;
        forward only;
        forwarders { 172.16.16.10; };
};
```

{% hint style="info" %}
For the reverse lookup zone, please ensure you use the first three octets of your subnet in reverse order, as shown in the example above. Example: Subnet: 192.168.1. Then, define the zone as `1.168.192.in-addr.arpa` IN {}.
{% endhint %}

3.  Run the following command to check if the **named.conf.recursive** have errors&#x20;

    `doas named-checkconf /etc/bind/named.conf.recursive`&#x20;
4. Restart the bind service using `doas rc-service named restart`&#x20;
5.  Update your Recursive DNS Server nameserver using the command below

    `echo "nameserver 172.16.16.16" | doas tee /etc/resolv.conf`

## Testing the Recursive DNS Server

Perform the following queries below to see if we can get a google like DNS responses.

#### Result from NSLookup for Google dns 8.8.8.8

```bash
dns:~$ nslookup 8.8.8.8
8.8.8.8.in-addr.arpa    name = dns.google.

Authoritative answers can be found from:

dns:~$ nslookup dns.google
Server:         172.16.16.16
Address:        172.16.16.16#53

Non-authoritative answer:
Name:   dns.google
Address: 8.8.8.8
Name:   dns.google
Address: 8.8.4.4
Name:   dns.google
Address: 2001:4860:4860::8888
Name:   dns.google
Address: 2001:4860:4860::8844

```

#### Result for NSLookup for aidentrix 172.16.16.16

```bash
dns:~$ nslookup 172.16.16.16
16.16.16.172.in-addr.arpa       name = dns.aidentrix.

Authoritative answers can be found from:

dns:~$ nslookup dns.aidentrix
Server:         172.16.16.16
Address:        172.16.16.16#53

Non-authoritative answer:
Name:   dns.aidentrix
Address: 172.16.16.16
```

#### Querying our Authoritative DNS Server

```bash
dns:~$ nslookup 172.16.16.10
10.16.16.172.in-addr.arpa       name = aidentrix.lan.
10.16.16.172.in-addr.arpa       name = ns1.aidentrix.lan.

Authoritative answers can be found from:

dns:~$ nslookup aidentrix.lan
Server:         172.16.16.16
Address:        172.16.16.16#53

Non-authoritative answer:
Name:   aidentrix.lan
Address: 172.16.16.10

dns:~$ nslookup ns1.aidentrix.lan
Server:         172.16.16.16
Address:        172.16.16.16#53

Non-authoritative answer:
Name:   ns1.aidentrix.lan
Address: 172.16.16.10

```

We now have our own Authoritative and Recursive DNS server, functioning like a Google DNS, for our makeshift public WWW network! :tada:
