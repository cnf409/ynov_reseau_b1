# TP5 - Un ptit LAN à nous

## I. Setup

☀️ Uniquement avec des commandes, prouvez-que :

Client 1:
```
ip a
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:41:e4:ef brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.11/24 brd 10.5.1.255 scope global enp0s3
       valid_lft forever preferred_lft forever

cat /etc/hostname 
client1

ping 10.5.1.12
PING 10.5.1.12 (10.5.1.12) 56(84) bytes of data.
64 bytes from 10.5.1.12: icmp_seq=1 ttl=64 time=0.569 ms

--- 10.5.1.12 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.569/0.569/0.569/0.000 ms

vincent@client1:~$ ping 10.5.1.254
PING 10.5.1.254 (10.5.1.254) 56(84) bytes of data.
64 bytes from 10.5.1.254: icmp_seq=1 ttl=64 time=0.691 ms
```

Client 2:
```
ip a
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:bb:02:85 brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.12/24 brd 10.5.1.255 scope global enp0s3
       valid_lft forever preferred_lft forever

cat /etc/hostname 
client2

vincent@client2:~$ ping 10.5.1.11
PING 10.5.1.11 (10.5.1.11) 56(84) bytes of data.
64 bytes from 10.5.1.11: icmp_seq=1 ttl=64 time=0.514 ms

--- 10.5.1.11 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.514/0.514/0.514/0.000 ms

vincent@client2:~$ ping 10.5.1.254
PING 10.5.1.254 (10.5.1.254) 56(84) bytes of data.
64 bytes from 10.5.1.254: icmp_seq=1 ttl=64 time=0.575 ms
```

Routeur:
```
ip a
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:03:63:61 brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.254/24 brd 10.5.1.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe03:6361/64 scope link 
       valid_lft forever preferred_lft forever

cat /etc/hostname 
routeur1

[root@routeur1 ~]# ping 10.5.1.11
PING 10.5.1.11 (10.5.1.11) 56(84) bytes of data.
64 bytes from 10.5.1.11: icmp_seq=1 ttl=64 time=0.744 ms

--- 10.5.1.11 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.744/0.744/0.744/0.000 ms

[root@routeur1 ~]# ping 10.5.1.12
PING 10.5.1.12 (10.5.1.12) 56(84) bytes of data.
64 bytes from 10.5.1.12: icmp_seq=1 ttl=64 time=0.962 ms
```

## II. Accès internet pour tous

### 1. Accès internet routeur
```
[root@routeur1 ~]# ping google.com
PING google.com (172.217.20.206) 56(84) bytes of data.
64 bytes from waw02s08-in-f206.1e100.net (172.217.20.206): icmp_seq=1 ttl=63 time=229 ms
64 bytes from waw02s08-in-f14.1e100.net (172.217.20.206): icmp_seq=2 ttl=63 time=142 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 142.040/185.768/229.497/43.728 ms

sudo firewall-cmd --add-masquerade --permanent
sudo firewall-cmd --reload
```

### 2. Accès internet clients

☀️ Prouvez que les clients ont un accès internet
```
vincent@client1:~$ ping google.com
PING google.com (142.251.221.14) 56(84) bytes of data.
64 bytes from mnl08s02-in-f14.1e100.net (142.251.221.14): icmp_seq=1 ttl=61 time=347 ms
^C
--- google.com ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 346.525/346.525/346.525/0.000 ms

ping google.com
PING google.com (142.250.201.174) 56(84) bytes of data.
64 bytes from par21s23-in-f14.1e100.net (142.250.201.174): icmp_seq=1 ttl=61 time=135 ms
^C
--- google.com ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 135.325/135.325/135.325/0.000 ms
```

☀️ Montrez-moi le contenu final du fichier de configuration de l'interface réseau
```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses: [10.5.1.12/24]
      gateway4: 10.5.1.254
      nameservers:
        addresses: [1.1.1.1]
```

## III. Serveur SSH

☀️ Sur routeur.tp5.b1, déterminer sur quel port écoute le serveur SSH
```
sudo ss -lnpt | grep 22
LISTEN 0      128          0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=711,fd=3))
LISTEN 0      128             [::]:22           [::]:*    users:(("sshd",pid=711,fd=4))
```

☀️ Sur routeur.tp5.b1, vérifier que ce port est bien ouvert
```
sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 22/tcp
  protocols: 
  forward: yes
  masquerade: yes
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules:
  ```

## IV. Serveur DHCP

### 3. Rendu attendu

#### A. Installation et configuration du serveur DHCP

☀️ Installez et configurez un serveur DHCP sur la machine routeur.tp5.b1
```
dnf -y install dhcp-server 

nano /etc/dhcp/dhcpd.conf

#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
option domain-name     "routeur1"; 

option domain-name-servers     1.1.1.1; 

authoritative; 

subnet 10.5.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.5.1.137 10.5.1.237;
    # specify broadcast address
    option broadcast-address 10.5.1.255;
    # specify gateway
    option routers 10.5.1.254;
}

systemctl enable --now dhcpd 

firewall-cmd --add-service=dhcp 

firewall-cmd --runtime-to-permanent 
```

#### B. Test avec un nouveau client

☀️ Créez une nouvelle machine client client3.tp5.b1
```
sudo hostnamectl set-hostname client3

sudo nano /etc/netplan/01-netcfg.yaml
*passage de dhcp4 en yes*

ip a 
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:08:be:8b brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.137/24 metric 100 brd 10.5.1.255 scope global dynamic enp0s3
       valid_lft 43166sec preferred_lft 43166sec
    inet6 fe80::a00:27ff:fe08:be8b/64 scope link 
       valid_lft forever preferred_lft forever

ping google.com
PING google.com (142.251.221.14) 56(84) bytes of data.
64 bytes from mnl08s02-in-f14.1e100.net (142.251.221.14): icmp_seq=1 ttl=61 time=500 ms
^C
--- google.com ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 499.885/499.885/499.885/0.000 ms
```

#### C. Consulter le bail DHCP

☀️ Consultez le bail DHCP qui a été créé pour notre client
```
cat /var/lib/dhcpd/dhcpd.leases 

lease 10.5.1.137 {
  starts 1 2024/10/14 15:12:41;
  ends 2 2024/10/15 03:12:41;
  cltt 1 2024/10/14 15:12:41;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 08:00:27:08:be:8b;
  uid "\377\3424?>\000\002\000\000\253\021\373'[\364\201j\011f";
  client-hostname "client3";
}
```

☀️ Confirmez qu'il s'agit bien de la bonne adresse MAC
```
ip a
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:08:be:8b brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.137/24 metric 100 brd 10.5.1.255 scope global dynamic enp0s3
       valid_lft 43019sec preferred_lft 43019sec
    inet6 fe80::a00:27ff:fe08:be8b/64 scope link 
       valid_lft forever preferred_lft forever
```

Adresse MAC -> `08:00:27:08:be:8b`

###### C'est super j'adore le réseau
