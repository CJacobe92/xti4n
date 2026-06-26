# Authoritative DNS Server Setup

We will now configure our authoritative DNS server to answer DNS queries for our home lab network infrastructure.

## Installing Bind9 on Alpine

1.  On the alpine terminal perform the following command to update the repositories

    `doas apk update`&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (8).png" alt="" width="441"><figcaption></figcaption></figure></div>
2.  Install bind9 using `doas apk add bind`&#x20;



    <div align="left"><figure><img src="../../.gitbook/assets/image (9).png" alt="" width="308"><figcaption></figcaption></figure></div>
3.  Confirm that the bind9 is properly by issuing `named -v`

    <div align="left"><figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure></div>

## Configuring named.conf

1. Create your **named.conf** file using `doas touch /etc/bind/named.conf`&#x20;
2.  Reference the **named.conf.authoritative** on your **named.conf** file

    `doas echo 'include "/etc/bind/named.conf.authoritative";' | doas tee /etc/bind/named.conf`&#x20;
3. Checked the named.conf if the configuration you added is correct by typing `doas named-checkconf /etc/bind/named.conf`. If there are no errors means your config if correct and has no typographical errors

## Configuring named.conf.authoritative

1.  Open your **named.conf.authoritative** for editing using the nano editor

    `doas nano /etc/bind/named.conf.authoritative`
2. Use the following configuration below:

```bash
options {
        directory "/var/bind";
        listen-on { 127.0.0.1; };
        listen-on-v6 { none; };
        
        allow-transfer { none; };
        pid-file "/var/run/named/named.pid";
        
        dnssec-validation no;
        allow-recursion { none; };
        recursion no;
};

zone "aidentrix.lan" IN {
        type primary;
        file "/var/bind/aidentrix.lan/db.forward";
        allow-query { any; };
        allow-update { none; };
};

zone "aidentrix" IN {
        type primary;
        file "/var/bind/aidentrix.lan/db.forward";
        allow-query { any; };
        allow-update { none; };
};

zone "16.16.172.in-addr.arpa" IN {
        type primary;
        file "/var/bind/aidentrix.lan/db.reverse";
        allow-query { any; };
        allow-update { none; };
};
```

{% hint style="info" %}
**For the reverse lookup zone, please ensure you use the first three octets of your subnet in reverse order, as shown in the example above. Example: Subnet: 192.168.1. Then, define the zone as** `1.168.192.in-addr.arpa` **IN {}.**
{% endhint %}

3.  Run the following command to check if the **named.conf.authoritative** have errors&#x20;

    `doas named-checkconf /etc/bind/named.conf.authoritative`&#x20;

## Create the forward lookup zone

1.  Create the folder where we are going to save the forward and reverse lookup zone files

    `doas mkdir /var/bind/aidentrix.lan`&#x20;
2. Create the file **db.forward** using `doas nano /var/bind/aidentrix.lan/db.forward`&#x20;
3. Use the following configuration below:

```bash
$TTL 604800
; SOA record with MNAME and RNAME updated
@       IN      SOA     ns1.aidentrix.lan. root.aidentrix.lan. (
                                3          ; Serial Note: increment after each change
                           604800          ; Refresh
                            86400          ; Retry
                          2419200          ; Expire
                           604800 )        ; Negative Cache TTL

; Name server record
@       IN      NS      ns1.aidentrix.lan.

; A records
@       IN      A       172.16.16.10
ns1     IN      A       172.16.16.10
www     IN      A       172.16.16.10
mail    IN      A       172.16.16.10

; MX record
@       IN      MX 10   mail.aidentrix.lan

```

4.  Perform a check if the configuration is correct using the command below&#x20;

    `doas named-checkzone aidentrix.lan /var/bind/aidentrix.lan/db.forward`&#x20;



    <figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

## Create the reverse lookup zone

1. Create the file **db.reverse** using `doas nano /var/bind/aidentrix.lan/db.reverse`&#x20;
2. Use the following configuration below:

```bash

; SOA record with MNAME and RNAME updated
@       IN      SOA     ns1.aidentrix.lan. root.aidentrix.lan. (
                              3         ; Serial Note: increment after each change
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
; Name server record
@      IN      NS      ns1.aidentrix.lan.


; PTR records
10     IN      PTR     ns1.aidentrix.lan.
10     IN      PTR     aidentrix.lan.

```

4.  Perform a check if the configuration is correct using the command below&#x20;

    `doas named-checkzone 16.16.172.in-addr.arpa /var/bind/aidentrix.lan/db.reverse`&#x20;



    <figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>
5. Restarted the bind service using `doas rc-service named restart`

{% hint style="info" %}
We need to update the name server on our Authoritative DNS server after performing all the setup above using the command below

`echo "nameserver 172.16.16.10" | doas tee /etc/resolv.conf`
{% endhint %}

## Testing the Authoritative DNS Server

Perform the following queries below and make they return the same result

#### nslookup for aidentrix.lan

```bash
ns1:~$ nslookup aidentrix.lan
;; Got recursion not available from 172.16.16.10
Server:         172.16.16.10
Address:        172.16.16.10#53

Name:   aidentrix.lan
Address: 172.16.16.10
;; Got recursion not available from 172.16.16.10
```

#### nslookup for ns1.aidentrix.lan

```bash
ns1:~$ nslookup ns1.aidentrix.lan
;; Got recursion not available from 172.16.16.10
Server:         172.16.16.10
Address:        172.16.16.10#53

Name:   ns1.aidentrix.lan
Address: 172.16.16.10
;; Got recursion not available from 172.16.16.10
```

#### nslookup for 172.16.16.10

```bash
ns1:~$ nslookup 172.16.16.10
;; Got recursion not available from 172.16.16.10
10.16.16.172.in-addr.arpa       name = aidentrix.lan.
10.16.16.172.in-addr.arpa       name = ns1.aidentrix.lan.
```

