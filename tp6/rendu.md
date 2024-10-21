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

# TP6 - Bonus Scapy

## 1. Play it legit

🌞 ping.py

```
vincent@client:~$ sudo python3 ping.py 
Begin emission
.......
Finished sending 1 packets
.*
Received 9 packets, got 1 answers, remaining 0 packets
Pong reçu : QueryAnswer(query=<Ether  dst=08:00:27:0c:bd:f8 src=08:00:27:37:f1:ff type=IPv4 |<IP  frag=0 proto=icmp src=10.6.1.37 dst=10.6.1.254 |<ICMP  type=echo-request |>>>, answer=<Ether  dst=08:00:27:37:f1:ff src=08:00:27:0c:bd:f8 type=IPv4 |<IP  version=4 ihl=5 tos=0x0 len=28 id=31197 flags= frag=0 ttl=64 proto=icmp chksum=0xe9d5 src=10.6.1.254 dst=10.6.1.37 |<ICMP  type=echo-reply code=0 chksum=0x0 id=0x0 seq=0x0 unused=b'' |<Padding  load=b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00' |>>>>)
```

🌞 dns_request.py

```
###[ IP ]###
  version   = 4
  ihl       = 5
  tos       = 0xc0
  len       = 89
  id        = 64499
  flags     = 
  frag      = 0
  ttl       = 64
  proto     = icmp
  chksum    = 0x66c2
  src       = 10.6.1.254
  dst       = 10.6.1.37
```

🌞 dhcp request.py

(on lance le sniff puis le dhcp.py)
```
vincent@client:~$ sudo python3 sniff.py 
ACK reçu
```

-> `dhcp_scapy`

## 2. Maybe not

```
lease 10.6.1.37 {
  starts 1 2024/10/21 12:28:38;
  ends 2 2024/10/22 00:28:38;
  tstp 2 2024/10/22 00:28:38;
  cltt 1 2024/10/21 14:43:13;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 08:00:27:37:f1:ff;
  uid "\001\010\000'7\361\377";
  client-hostname "client";
}
lease 10.6.1.40 {
  starts 1 2024/10/21 13:55:46;
  ends 2 2024/10/22 01:55:46;
  cltt 1 2024/10/21 14:19:36;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 08:00:27:37:f1:ff;
}
[...]
lease 10.6.1.135 {
  starts 1 2024/10/21 14:53:05;
  ends 2 2024/10/22 02:53:05;
  cltt 1 2024/10/21 14:53:05;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 34:35:39:30:34:62;
}
lease 10.6.1.136 {
  starts 1 2024/10/21 14:53:06;
  ends 2 2024/10/22 02:53:06;
  cltt 1 2024/10/21 14:53:06;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 31:31:64:32:32:61;
}
```

(capture à l'appui -> `starve.pcap`)