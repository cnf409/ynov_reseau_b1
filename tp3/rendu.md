# TP3 - 32°13'34"N 95°03'27"W

## I. ARP basics

☀️ Avant de continuer...

On utilise `ip a`: 
```
90:e8:68:15:ac:43
```

☀️ Affichez votre table ARP

On utilise `ip n s`:
```
10.33.68.54 dev wlo1 lladdr 6e:91:40:e2:af:1d STALE
10.33.79.254 dev wlo1 lladdr 7c:5a:1c:d3:d8:76 REACHABLE
10.33.67.119 dev wlo1 lladdr 60:6e:e8:2a:8f:90 STALE
10.33.65.63 dev wlo1 lladdr 44:af:28:c3:6a:9f STALE
```

☀️ Déterminez l'adresse MAC de la passerelle du réseau de l'école

L'ip de la passerelle est `10.33.79.254` , on voit sur la commande précédente que l'adresse MAC est:
```
7c:5a:1c:d3:d8:76
```

☀️ Supprimez la ligne qui concerne la passerelle

On utilise `sudo ip n del to 10.33.79.254 dev wlo1` (mais elle revient direct)

☀️ Prouvez que vous avez supprimé la ligne dans la table ARP
```
sudo ip n del to 10.33.79.254 dev wlo1; ip n s

10.33.77.206 dev wlo1 lladdr b8:1e:a4:6c:56:97 STALE
10.33.65.63 dev wlo1 lladdr 44:af:28:c3:6a:9f STALE
```

☀️ Wireshark

-> `arp1.pcap`

## II. ARP dans un réseau local

### 1. Basics

☀️ Déterminer

Adresse IP: `192.168.11.228/24`

Adresse MAC: `90:e8:68:15:ac:43` (toujours la même)

☀️ DIY

Adresse DNS & Passerelle: `192.168.11.46` (`resolvectl dns`)(la même car le téléphone fait les deux)

Lister les IP disponibles: `nmap -sn -PR 192.168.11.0/24`

Supprimer l'IP: `sudo ip a delete 192.168.11.228/24 dev wlo1`

Ajouter la nouvelle IP: `sudo ip a add 192.168.11.69/24 dev wlo1`

☀️ Pingz !

```
ping 192.168.11.30

PING 192.168.11.30 (192.168.11.30) 56(84) bytes of data.
64 bytes from 192.168.11.30: icmp_seq=1 ttl=128 time=40.1 ms
64 bytes from 192.168.11.30: icmp_seq=2 ttl=128 time=11.3 ms
64 bytes from 192.168.11.30: icmp_seq=3 ttl=128 time=6.52 ms
64 bytes from 192.168.11.30: icmp_seq=4 ttl=128 time=4.48 ms
^C
--- 192.168.11.30 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 4.484/15.598/40.095/14.357 ms
```

Vérification de l'accès à internet: 
```
ping google.com

PING google.com(par21s18-in-x0e.1e100.net (2a00:1450:4007:806::200e)) 56 data bytes
64 bytes from par21s18-in-x0e.1e100.net (2a00:1450:4007:806::200e): icmp_seq=1 ttl=112 time=204 ms
64 bytes from par21s18-in-x0e.1e100.net (2a00:1450:4007:806::200e): icmp_seq=2 ttl=112 time=227 ms
64 bytes from par21s18-in-x0e.1e100.net (2a00:1450:4007:806::200e): icmp_seq=3 ttl=112 time=248 ms
64 bytes from par21s18-in-x0e.1e100.net (2a00:1450:4007:806::200e): icmp_seq=4 ttl=112 time=271 ms
^C
--- google.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 203.934/237.538/271.398/25.012 ms
```

### 2. ARP

☀️ Affichez votre table ARP !
```
ip n s

192.168.11.47 dev wlo1 lladdr 58:cd:c9:60:e5:fb STALE
192.168.11.7 dev wlo1 lladdr 34:c9:3d:22:97:2d STALE
192.168.11.46 dev wlo1 lladdr 96:24:c9:47:ee:dc REACHABLE
192.168.11.30 dev wlo1 lladdr 14:5a:fc:7f:13:93 STALE
2a02:8440:6440:373b::3d dev wlo1 lladdr 96:24:c9:47:ee:dc router STALE
fe80::9424:c9ff:fe47:eedc dev wlo1 lladdr 96:24:c9:47:ee:dc router REACHABLE
```

➜ Wireshark that

Vider la table ARP:
```
sudo ip link set arp off dev wlo1; sudo ip link set arp on dev wlo1
```

☀️ Capture arp2.pcap

-> `arp2.pcap`

### 3. (pas)Bonus : ARP poisoning

On lance le script `spoofing.py` puis on lance `ip n s` sur l'ordinateur de la victime

```
> ip n s
172.20.10.4 dev wlp3s0 lladdr 90:e8:68:15:ac:43 STALE 
172.20.10.1 dev wlp3s0 lladdr 90:e8:68:15:ac:43 REACHABLE 
fe80::60d0:39ff:fef1:3f64 dev wlp3s0 lladdr 62:d0:39:f1:3f:64 router STALE 
2a0d:e487:132f:e506:f40d:223a:e036:824e dev wlp3s0 lladdr 62:d0:39:f1:3f:64 router STALE 
```

La ligne `172.20.10.1 dev wlp3s0 lladdr 90:e8:68:15:ac:43 REACHABLE ` montre que l'adresse MAC
associée à l'adresse IP du routeur est bien la mienne.

Pour le MITM, il faut activer l'IP Forwarding: `sysctl -w net.ipv4.ip_forward=1`.

Les packets qui ne sont pas destinés à ma machine seront donc "forwardés" vers le bon destinataire.

On peut voir dans le fichier `spoofing.pcap` que je reçois les requêtes que la victime voulait envoyer. 

En revanche, je ne reçois pas les réponses. C'est (à priori) dû à une protection du routeur contre 
l'arp spoofing, ce qui fait que je reçois les requêtes que la victime veut envoyer au routeur, mais pas
les réponses du routeur, elles sont directement envoyées à la victime
(car la table ARP du routeur n'est pas modifiée).
