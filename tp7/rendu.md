# TP7 - On dit chiffrer pas crypter

## II. Serveur Web


### 1. HTTP

#### B. Configuration

🌞 Lister les ports en écoute sur la machine

```
[root@web ~]# ss -lnp | grep "nginx"
tcp   LISTEN 0      511                                       0.0.0.0:80               0.0.0.0:*    users:(("nginx",pid=11279,fd=6),("nginx",pid=11278,fd=6))                                    
tcp   LISTEN 0      511                                          [::]:80                  [::]:*    users:(("nginx",pid=11279,fd=7),("nginx",pid=11278,fd=7)
```

🌞 Ouvrir le port dans le firewall de la machine

```
sudo firewall-cmd --permanent --add-port=8888/tcp
sudo firewall-cmd --reload

[root@web ~]# sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 80/tcp
  protocols: 
  forward: yes
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules:
```

#### C. Tests client

🌞 Vérifier que ça a pris effet

```
vincent@vinent-vbox:~$ ping sitedefou.tp7.b1
PING sitedefou.tp7.b1 (10.7.1.11) 56(84) bytes of data.
64 bytes from sitedefou.tp7.b1 (10.7.1.11): icmp_seq=1 ttl=64 time=0.599 ms
^C
--- sitedefou.tp7.b1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.599/0.599/0.599/0.000 ms

vincent@vinent-vbox:~$ curl sitedefou.tp7.b1
rami malek nous contrôle tous

(j'ai pas écrit meow dans la page désolé)

```

#### D. Analyze

🌞 Capture tcp_http.pcap

-> `tcp_http.pcap`

🌞 Voir la connexion établie

```
vincent@vinent-vbox:~$ sudo ss -tnp | grep "10.7.1.11"
ESTAB 0      0               10.7.1.101:53546         10.7.1.11:80    users:(("firefox",pid=5033,fd=115))
```

### 2. On rajoute un S

#### A. Config

🌞 Lister les ports en écoute sur la machine

```
[root@web ~]# ss -tlnp | grep "nginx"
LISTEN 0      511        10.7.1.11:443       0.0.0.0:*    users:(("nginx",pid=11397,fd=6),("nginx",pid=11396,fd=6))
```

🌞 Gérer le firewall

```
[root@web ~]# sudo firewall-cmd --permanent --add-port=443/tcp
success
[root@web ~]# sudo firewall-cmd --permanent --remove-port=80/tcp
success
[root@web ~]# sudo firewall-cmd --reload
success
```

#### B. Test test test analyyyze

```
vincent@vinent-vbox:~$ curl -k https://sitedefou.tp7.b1
rami malek nous contrôle tous
```

🌞 Capture tcp_https.pcap