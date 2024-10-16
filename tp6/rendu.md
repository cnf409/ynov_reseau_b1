# TP6 - Des bo services dans des bo LANs

## I. Le setup

### 2. Marche à suivre

☀️ Prouvez que...

Machine du LAN1 (dhcp):
```
[root@dhcp ~]# ping archive.org
PING archive.org (207.241.224.2) 56(84) bytes of data.
64 bytes from www.archive.org (207.241.224.2): icmp_seq=1 ttl=61 time=197 ms
^C
--- archive.org ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 196.671/196.671/196.671/0.000 ms
```

Machine du LAN2 (dns):
```
[root@dns ~]# ping archive.org
PING archive.org (207.241.224.2) 56(84) bytes of data.
64 bytes from www.archive.org (207.241.224.2): icmp_seq=1 ttl=61 time=231 ms
^C
--- archive.org ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 230.553/230.553/230.553/0.000 ms
```

Machine du LAN1 ping une machine du LAN2 (dhcp -> dns):
```
[root@dhcp ~]# ping 10.6.2.12
PING 10.6.2.12 (10.6.2.12) 56(84) bytes of data.
64 bytes from 10.6.2.12: icmp_seq=1 ttl=63 time=0.781 ms
^C
--- 10.6.2.12 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.781/0.781/0.781/0.000 ms
```

## II. LAN clients

### 2. Client

☀️ Prouvez que...

IP:
```
vincent@client:~$ ip a
[...]
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:37:f1:ff brd ff:ff:ff:ff:ff:ff
    inet 10.6.1.37/24 brd 10.6.1.255 scope global dynamic noprefixroute enp0s3
       valid_lft 43109sec preferred_lft 43109sec
    inet6 fe80::a00:27ff:fe37:f1ff/64 scope link 
       valid_lft forever preferred_lft forever

```

DNS:
```
vincent@client:~$ resolvectl
Global
         Protocols: -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
  resolv.conf mode: stub

Link 2 (enp0s3)
    Current Scopes: DNS
         Protocols: +DefaultRoute -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
Current DNS Server: 1.1.1.1
       DNS Servers: 1.1.1.1
```

Passerelle:
```
vincent@client:~$ ip route show
default via 10.6.1.254 dev enp0s3 proto dhcp src 10.6.1.37 metric 100 
10.6.1.0/24 dev enp0s3 proto kernel scope link src 10.6.1.37 metric 100
```

Ping:
```
vincent@client:~$ ping archive.org
PING archive.org (207.241.224.2) 56(84) bytes of data.
64 bytes from www.archive.org (207.241.224.2): icmp_seq=1 ttl=61 time=242 ms
^C
--- archive.org ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 241.824/241.824/241.824/0.000 ms
```

## III. LAN serveurzzzz

### 1. Serveur Web

☀️ Déterminer sur quel port écoute le serveur NGINX

```
[root@web ~]# sudo ss -lnpt | grep 80
LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=6592,fd=6),("nginx",pid=6590,fd=6))
LISTEN 0      511             [::]:80           [::]:*    users:(("nginx",pid=6592,fd=7),("nginx",pid=6590,fd=7))
```

☀️ Ouvrir ce port dans le firewall

```
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload
```

☀️ Visitez le site web !
```
[root@dhcp ~]# curl 10.6.2.11
<!doctype html>
<html>
[...]
```

### 2. Serveur DNS

☀️ Déterminer sur quel(s) port(s) écoute le service BIND9
```
[root@dns ~]# ss -lnpt | grep 53
LISTEN 0      10         127.0.0.1:53        0.0.0.0:*    users:(("named",pid=2035,fd=22))
LISTEN 0      4096       127.0.0.1:953       0.0.0.0:*    users:(("named",pid=2035,fd=26))
LISTEN 0      4096           [::1]:953          [::]:*    users:(("named",pid=2035,fd=27))
LISTEN 0      10             [::1]:53           [::]:*    users:(("named",pid=2035,fd=25))
```

☀️ Ouvrir ce(s) port(s) dans le firewall
```
sudo firewall-cmd --permanent --add-port=53/tcp
sudo firewall-cmd --permanent --add-port=53/udp
sudo firewall-cmd --reload
```

☀️ Effectuez des requêtes DNS manuellement depuis le serveur DNS lui-même dans un premier temps

```
;; ANSWER SECTION:
web.tp6.b1.		86400	IN	A	10.6.2.11
```
```
;; ANSWER SECTION:
dns.tp6.b1.		86400	IN	A	10.6.2.12
```

Reverse:
```
;; ANSWER SECTION:
11.2.6.10.in-addr.arpa.	86400	IN	PTR	web.tp6.b1.
```
```
;; ANSWER SECTION:
12.2.6.10.in-addr.arpa.	86400	IN	PTR	dns.tp6.b1.
```

☀️ Effectuez une requête DNS manuellement depuis client1.tp6.b1
```
vincent@client:~$ dig web.tp6.b1 @10.6.2.12
[...]
;; ANSWER SECTION:
web.tp6.b1.		86400	IN	A	10.6.2.11
```

☀️ Capturez une requête DNS et la réponse de votre serveur
-> `dns.pcap`

### 3. Serveur DHCP

☀️ Créez un nouveau client client2.tp6.b1 vitefé
```
vincent@vinent-vbox:~$ curl web.tp6.b1
<!doctype html>
<html>
```