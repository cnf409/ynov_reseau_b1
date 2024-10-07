# TP2 - Hey yo tell a neighbor tell a friend

## I. Simplest LAN

TP fait avec la VM car pas de port ethernet & pas d'amis

### 1. Quelques pings

🌞 Prouvez que votre configuration est effective
```
11: vboxnet0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 0a:00:27:00:00:00 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.1/24 brd 192.168.56.255 scope global vboxnet0
       valid_lft forever preferred_lft forever
    inet6 fe80::800:27ff:fe00:0/64 scope link 
       valid_lft forever preferred_lft forever
```

🌞 Tester que votre LAN + votre adressage IP est fonctionnel
```
MACHINE LOCAL
PING 192.168.56.104 (192.168.56.104) 56(84) bytes of data.
64 bytes from 192.168.56.104: icmp_seq=1 ttl=64 time=0.620 ms
64 bytes from 192.168.56.104: icmp_seq=2 ttl=64 time=0.590 ms
64 bytes from 192.168.56.104: icmp_seq=3 ttl=64 time=0.615 ms
64 bytes from 192.168.56.104: icmp_seq=4 ttl=64 time=0.642 ms
64 bytes from 192.168.56.104: icmp_seq=5 ttl=64 time=0.536 ms
^C
--- 192.168.56.104 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4119ms

MACHINE VM
PING 192.168.56.1 (192.168.56.1) 56(84) bytes of data.
64 bytes from 192.168.56.1: icmp_seq=1 ttl=64 time=0.470 ms
64 bytes from 192.168.56.1: icmp_seq=2 ttl=64 time=0.416 ms
64 bytes from 192.168.56.1: icmp_seq=3 ttl=64 time=0.357 ms
64 bytes from 192.168.56.1: icmp_seq=4 ttl=64 time=0.345 ms
^C
--- 192.168.56.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3089ms
```

🌞 Capture de ping
-> `ping.pcap`

## II. Utilisation des ports

### 1. Animal numérique

🌞 Sur le PC serveur

Autoriser les connections sur le port 6942 : `sudo ufw allow 6942/tcp`

Lancer l'écoute sur le port 6942 (haha drole): `nc -l 6942`

```
sudo ss -lnpt
State       Recv-Q      Send-Q           Local Address:Port           Peer Address:Port      Process                                                                                      
LISTEN      0           1                      0.0.0.0:6942                0.0.0.0:*          users:(("nc",pid=4174,fd=3))                                                                
LISTEN      0           4096             127.0.0.53%lo:53                  0.0.0.0:*          users:(("systemd-resolve",pid=451,fd=15))                                                   
LISTEN      0           4096                 127.0.0.1:631                 0.0.0.0:*          users:(("cupsd",pid=1375,fd=7))                                                             
LISTEN      0           4096                127.0.0.54:53                  0.0.0.0:*          users:(("systemd-resolve",pid=451,fd=17))                                                   
LISTEN      0           4096                         *:22                        *:*          users:(("sshd",pid=1401,fd=3),("systemd",pid=1,fd=262))                                     
LISTEN      0           4096                     [::1]:631                    [::]:*          users:(("cupsd",pid=1375,fd=6))                                                             
LISTEN      0           511                          *:8080                      *:*          users:(("apache2",pid=1408,fd=3),("apache2",pid=1407,fd=3),("apache2",pid=1405,fd=3)) 
```

🌞 Sur le PC client
```
nc 192.168.56.104 6942
test machine
test client
```
🌞 Echangez-vous des messages
```
nc -lvp 6942
Listening on 0.0.0.0 6942
Connection received on pasta-box 40054
test machine
test client
```

🌞 Utilisez une commande qui permet de voir la connexion en cours

Sur le client:
```
ESTAB     0          0                192.168.56.1:40054            192.168.56.104:6942       users:(("nc",pid=82335,fd=3)) 
```

Sur le serveur:
```
State   Recv-Q   Send-Q               Local Address:Port                Peer Address:Port    Process                                                                                      
ESTAB   0        0                   192.168.56.104:6942                192.168.56.1:40054    users:(("nc",pid=4503,fd=4))
```

🌞 Faites une capture Wireshark complète d'un échange
-> `netcat1.pcap`

🌞 Inversez les rôles

Sur le serveur:
```
nc -lvp 4269
Listening on 0.0.0.0 4269
Connection received on 192.168.56.104 38730

ESTAB     0          0                192.168.56.1:4269             192.168.56.104:38730      users:(("nc",pid=83744,fd=4))
```

Sur le client:
```
ESTAB   0        0                   192.168.56.104:38730               192.168.56.1:4269     users:(("nc",pid=4728,fd=3))
```

## III. Analyse de vos applications usuelles

### 1. Serveur web

🌞 Utilisez Wireshark pour capturer du trafic HTTP
-> `http.pcap`

### 2. Autres services

- Discord:
```
sudo ss -tnp | grep -i "discord"                                                                      ✔ ╱ took 3s  ╱ at 17:03:58  
ESTAB      0      0           10.33.69.60:56752 162.159.137.234:443   users:(("Discord",pid=92471,fd=34))                                            
ESTAB      0      0           10.33.69.60:36386 162.159.135.233:443   users:(("Discord",pid=92471,fd=38))                                            
ESTAB      0      0           10.33.69.60:60998 162.159.135.234:443   users:(("Discord",pid=92471,fd=29))                                            
ESTAB      0      0           10.33.69.60:44050 162.159.128.233:443   users:(("Discord",pid=92471,fd=36))                                            
ESTAB      0      0           10.33.69.60:60428     2.16.149.76:443   users:(("Discord",pid=92471,fd=39))                                            
ESTAB      0      0           10.33.69.60:48846   35.186.224.24:443   users:(("Discord",pid=92471,fd=35))                                            
ESTAB      0      0           10.33.69.60:59974   35.186.224.45:443   users:(("Discord",pid=92471,fd=37))

Adresses IP: 162.159.137.234, 162.159.135.233, 162.159.135.234, 162.159.128.233, 2.16.149.76, 35.186.224.24, 35.186.224.45
Ports: 443 
```

- Spotify:
```
sudo ss -tnp | grep -i "spotify"                                                                                  ✔ ╱ at 17:12:47  
ESTAB 0      0        10.33.69.60:55104 199.232.214.248:443   users:(("spotify",pid=94611,fd=55))                                            
ESTAB 0      0        10.33.69.60:55154 199.232.214.248:443   users:(("spotify",pid=94611,fd=68))                                            
ESTAB 0      0        10.33.69.60:47598   35.186.224.24:443   users:(("spotify",pid=94611,fd=23))                                            
ESTAB 0      0        10.33.69.60:49924     2.16.149.81:443   users:(("spotify",pid=94611,fd=46))                                            
ESTAB 0      0        10.33.69.60:53502   35.186.224.41:443   users:(("spotify",pid=94611,fd=35))                                            
ESTAB 0      0        10.33.69.60:55120 199.232.214.248:443   users:(("spotify",pid=94611,fd=56))                                            
ESTAB 0      0        10.33.69.60:59408    104.199.65.9:4070  users:(("spotify",pid=94384,fd=85))                                            
ESTAB 0      0        10.33.69.60:55142 199.232.214.248:443   users:(("spotify",pid=94611,fd=66))                                            
ESTAB 0      0        10.33.69.60:49588   172.217.18.34:443   users:(("spotify",pid=94611,fd=47))                                            
ESTAB 0      0        10.33.69.60:42698 199.232.214.250:443   users:(("spotify",pid=94611,fd=91))                                            
ESTAB 0      0        10.33.69.60:47618   35.186.224.24:443   users:(("spotify",pid=94611,fd=43))                                            
ESTAB 0      0        10.33.69.60:54826   142.251.37.33:443   users:(("spotify",pid=94611,fd=45))                                            
ESTAB 0      0        10.33.69.60:55146 199.232.214.248:443   users:(("spotify",pid=94611,fd=67))                                            
ESTAB 0      0        10.33.69.60:38712 199.232.210.250:443   users:(("spotify",pid=94611,fd=42))                                            
ESTAB 0      0        10.33.69.60:42022     2.16.149.84:443   users:(("spotify",pid=94611,fd=48))                                            
ESTAB 0      0        10.33.69.60:52050  142.251.37.227:443   users:(("spotify",pid=94611,fd=57))                                            
ESTAB 0      0        10.33.69.60:55136 199.232.214.248:443   users:(("spotify",pid=94611,fd=59))                                            
ESTAB 0      0        10.33.69.60:59082  142.251.37.228:443   users:(("spotify",pid=94611,fd=54))                                            
ESTAB 0      0        10.33.69.60:60950   35.186.224.26:443   users:(("spotify",pid=94611,fd=30))                                            
ESTAB 0      0        10.33.69.60:55158 199.232.214.248:443   users:(("spotify",pid=94611,fd=69))                                            
ESTAB 0      0        10.33.69.60:48802     23.1.106.35:443   users:(("spotify",pid=94611,fd=49))                                            
ESTAB 0      0        10.33.69.60:47604   35.186.224.24:443   users:(("spotify",pid=94611,fd=24))                                            
ESTAB 0      0        10.33.69.60:53500   35.186.224.41:443   users:(("spotify",pid=94384,fd=87))                                            
ESTAB 0      0        10.33.69.60:48800     23.1.106.35:443   users:(("spotify",pid=94611,fd=44))                                            
ESTAB 0      0        10.33.69.60:57190 199.232.210.250:443   users:(("spotify",pid=94611,fd=77))
```

- Firefox:
```
sudo ss -tnp | grep -i "firefox"                                                                                  ✔ ╱ at 17:16:21  
ESTAB      0      0        10.33.69.60:46924    50.19.172.40:443   users:(("firefox",pid=90891,fd=180))                                           
ESTAB      0      0        10.33.69.60:55342   34.107.243.93:443   users:(("firefox",pid=90891,fd=175))                                           
ESTAB      0      0        10.33.69.60:57128   172.65.251.78:443   users:(("firefox",pid=90891,fd=170))
```

- Minecraft:
```
sudo ss -tnp | grep -i "minecraft"                                                                                ✔ ╱ at 17:20:28  
ESTAB 0      0        10.33.69.60:47614   13.107.246.43:443   users:(("minecraft-launc",pid=96119,fd=79))                                    
ESTAB 0      0        10.33.69.60:33352   13.107.246.43:443   users:(("minecraft-launc",pid=96119,fd=95))                                    
ESTAB 0      0        10.33.69.60:47588   13.107.246.43:443   users:(("minecraft-launc",pid=96119,fd=100))                                   
ESTAB 0      0        10.33.69.60:47618   13.107.246.43:443   users:(("minecraft-launc",pid=96119,fd=87))                                    
ESTAB 0      0        10.33.69.60:49170   13.86.100.218:443   users:(("minecraft-launc",pid=96119,fd=63))                                    
ESTAB 0      0        10.33.69.60:44100   23.99.194.233:443   users:(("minecraft-launc",pid=96119,fd=26))                                    
ESTAB 0      0        10.33.69.60:47572   13.107.246.43:443   users:(("minecraft-launc",pid=96119,fd=94))                                    
ESTAB 0      0        10.33.69.60:55190    13.95.168.57:443   users:(("minecraft-launc",pid=96119,fd=18))                                    
ESTAB 0      0        10.33.69.60:41338    20.189.173.5:443   users:(("minecraft-launc",pid=96119,fd=89))                                    
ESTAB 0      0        10.33.69.60:56936      2.16.228.7:443   users:(("minecraft-launc",pid=96119,fd=76))                                    
ESTAB 0      0        10.33.69.60:47548   13.107.246.43:443   users:(("minecraft-launc",pid=96119,fd=88))                                    
ESTAB 0      0        10.33.69.60:47562   13.107.246.43:443   users:(("minecraft-launc",pid=96208,fd=27))                                    
ESTAB 0      0        10.33.69.60:47604   13.107.246.43:443   users:(("minecraft-launc",pid=96119,fd=101))                                   
ESTAB 0      0        10.33.69.60:33332   13.107.246.43:443   users:(("minecraft-launc",pid=96119,fd=93))                                    
ESTAB 0      0        10.33.69.60:33328   13.107.246.43:443   users:(("minecraft-launc",pid=96119,fd=92))
```

- TabNine (extention vscode):
```
sudo ss -tnp | grep -i "tabnine"                                                                                  ✔ ╱ at 17:26:35  
ESTAB 0      0        10.33.69.60:49398   34.123.33.186:443   users:(("TabNine",pid=92065,fd=18))                                            
ESTAB 0      0        10.33.69.60:49410   34.123.33.186:443   users:(("TabNine",pid=92120,fd=37))                                            
ESTAB 0      0          127.0.0.1:49153       127.0.0.1:39856 users:(("TabNine-deep-lo",pid=92231,fd=64),("TabNine-deep-lo",pid=92231,fd=23))
ESTAB 0      0          127.0.0.1:39856       127.0.0.1:49153 users:(("TabNine",pid=92120,fd=41),("TabNine",pid=92120,fd=40))                
ESTAB 0      0        10.33.69.60:49408   34.123.33.186:443   users:(("TabNine-deep-lo",pid=92231,fd=19))                                    
ESTAB 0      0        10.33.69.60:49416   34.123.33.186:443   users:(("TabNine",pid=92120,fd=23))                                            
ESTAB 0      0          127.0.0.1:49153       127.0.0.1:57638 users:(("TabNine-deep-lo",pid=92231,fd=24),("TabNine-deep-lo",pid=92231,fd=21))
ESTAB 0      0        10.33.69.60:49406   34.123.33.186:443   users:(("WD-TabNine",pid=92112,fd=19))                                         
ESTAB 0      0        10.33.69.60:34536    34.170.65.59:443   users:(("TabNine-deep-lo",pid=92231,fd=28))                                    
ESTAB 0      0        10.33.69.60:45130    34.170.65.59:443   users:(("TabNine",pid=92120,fd=42))                                            
ESTAB 0      0          127.0.0.1:57638       127.0.0.1:49153 users:(("TabNine",pid=92120,fd=38),("TabNine",pid=92120,fd=28))                
ESTAB 0      0        10.33.69.60:46210   34.123.33.186:443   users:(("TabNine",pid=92120,fd=29))                                            
ESTAB 0      0        10.33.69.60:49384   34.123.33.186:443   users:(("TabNine",pid=92120,fd=13))
```


###### tout les .pcap sont disponibles dans le fichier 10/10 j'adore le réseau