# Ce n'est pas mon rendu
suite à un malencontreux revert de commit j'ai perdu mon rendu pour ce tp... celui là est donc celui de dorian, desolé 👍 (sinon mes autres rendus sont super)

🌞 Adresses IP de ta machine

```
PS C:\Users\doria> ipconfig /all
Carte réseau sans fil Wi-Fi :

   Adresse IPv6 de liaison locale. . . . .: fe80::9999:d1b5:dbf0:97b3%23(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.77.157(préféré)



Carte Ethernet Ethernet 3 :

   Adresse IPv6 de liaison locale. . . . .: fe80::1629:16df:838d:a359%13(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 192.168.56.1(préféré)
``` 



🌞 Si t'as un accès internet normal, d'autres infos sont forcément dispos...

```
PS C:\Users\doria> ipconfig /all
Carte réseau sans fil Wi-Fi :

   Passerelle par défaut. . . . . . . . . : 10.33.79.254
   Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254
   Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                                       1.1.1.1
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé
```


🌟 BONUS : Détermine s'il y a un pare-feu actif sur ta machine

```
PS C:\Users\doria> Get-NetFirewallProfile | Select-Object Name, Enabled

Name    Enabled
----    -------
Domain     True
Private    True
Public     True


PS C:\Users\doria> Get-NetFirewallRule | Select-Object -First 1

Name                          : WFDPRINT-SPOOL-Out-Active
DisplayName                   : Utilisation de spouleur Wi-Fi Direct (Sortie)
Description                   : Règle de trafic sortant relative à l’utilisation d’imprimantes WSD sur des réseaux Wi-Fi Direct.
DisplayGroup                  : Découverte de réseau Wi-Fi Direct
Group                         : @FirewallAPI.dll,-36851
Enabled                       : True
Profile                       : Public
Platform                      : {}
Direction                     : Outbound
Action                        : Allow
EdgeTraversalPolicy           : Block
LooseSourceMapping            : False
LocalOnlyMapping              : False
Owner                         :
PrimaryStatus                 : OK
Status                        : La règle a été analysée à partir de la banque. (65536)
EnforcementStatus             : NotApplicable
PolicyStoreSource             : PersistentStore
PolicyStoreSourceType         : Local
RemoteDynamicKeywordAddresses : {}
PolicyAppId                   :
```



🌞 Envoie un ping vers...
```
PS C:\Users\doria> ping 10.33.77.157

Envoi d’une requête 'Ping'  10.33.77.157 avec 32 octets de données :
Réponse de 10.33.77.157 : octets=32 temps<1ms TTL=128
Réponse de 10.33.77.157 : octets=32 temps<1ms TTL=128
Réponse de 10.33.77.157 : octets=32 temps<1ms TTL=128
Réponse de 10.33.77.157 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 10.33.77.157:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms


PS C:\Users\doria> ping 127.0.0.1

Envoi d’une requête 'Ping'  127.0.0.1 avec 32 octets de données :
Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128
Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128
Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128
Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 127.0.0.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
```

🌞 On continue avec ping. Envoie un ping vers...
```
PS C:\Users\doria> ping 10.33.79.254

Envoi d’une requête 'Ping'  10.33.79.254 avec 32 octets de données :
Délai d’attente de la demande dépassé.

Statistiques Ping pour 10.33.79.254:
    Paquets : envoyés = 1, reçus = 0, perdus = 1 (perte 100%),
Ctrl+C


PS C:\Users\doria> ping 10.33.76.18

Envoi d’une requête 'Ping'  10.33.76.18 avec 32 octets de données :
Réponse de 10.33.76.18 : octets=32 temps=13 ms TTL=64
Réponse de 10.33.76.18 : octets=32 temps=21 ms TTL=64
Réponse de 10.33.76.18 : octets=32 temps=34 ms TTL=64
Réponse de 10.33.76.18 : octets=32 temps=18 ms TTL=64

Statistiques Ping pour 10.33.76.18:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 13ms, Maximum = 34ms, Moyenne = 21ms


    Minimum = 13ms, Maximum = 34ms, Moyenne = 21ms
PS C:\Users\doria> ping www.thinkerview.com

Envoi d’une requête 'ping' sur www.thinkerview.com [188.114.96.7] avec 32 octets de données :
Réponse de 188.114.96.7 : octets=32 temps=15 ms TTL=55
Réponse de 188.114.96.7 : octets=32 temps=15 ms TTL=55
Réponse de 188.114.96.7 : octets=32 temps=16 ms TTL=55
Réponse de 188.114.96.7 : octets=32 temps=16 ms TTL=55

Statistiques Ping pour 188.114.96.7:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 15ms, Maximum = 16ms, Moyenne = 15ms
```

🌞 Faire une requête DNS à la main

```
PS C:\Users\doria> Resolve-DnsName www.thinkerview.com

Name                                           Type   TTL   Section    IPAddress
----                                           ----   ---   -------    ---------
www.thinkerview.com                            AAAA   300   Answer     2a06:98c1:3121::7
www.thinkerview.com                            AAAA   300   Answer     2a06:98c1:3120::7
www.thinkerview.com                            A      300   Answer     188.114.97.7
www.thinkerview.com                            A      300   Answer     188.114.96.7

PS C:\Users\doria> Resolve-DnsName www.wikileaks.org

Name                           Type   TTL   Section    NameHost
----                           ----   ---   -------    --------
www.wikileaks.org              CNAME  1800  Answer     wikileaks.org

Name       : wikileaks.org
QueryType  : A
TTL        : 400
Section    : Answer
IP4Address : 80.81.248.21


Name       : wikileaks.org
QueryType  : A
TTL        : 400
Section    : Answer
IP4Address : 51.159.197.136


Name                   : wikileaks.org
QueryType              : SOA
TTL                    : 1800
Section                : Authority
NameAdministrator      : root.wlinfra.org
SerialNumber           : 2022082502
TimeToZoneRefresh      : 21600
TimeToZoneFailureRetry : 3600
TimeToExpiration       : 604800
DefaultTTL             : 1800

PS C:\Users\doria> Resolve-DnsName www.torproject.org

Name                                           Type   TTL   Section    IPAddress
----                                           ----   ---   -------    ---------
www.torproject.org                             AAAA   150   Answer     2620:7:6002:0:466:39ff:fe32:e3dd
www.torproject.org                             AAAA   150   Answer     2620:7:6002:0:466:39ff:fe7f:1826
www.torproject.org                             AAAA   150   Answer     2a01:4f9:c010:19eb::1
www.torproject.org                             AAAA   150   Answer     2a01:4f8:fff0:4f:266:37ff:fe2c:5d19
www.torproject.org                             AAAA   150   Answer     2a01:4f8:fff0:4f:266:37ff:feae:3bbc
www.torproject.org                             A      67    Answer     204.8.99.146
www.torproject.org                             A      67    Answer     116.202.120.165
www.torproject.org                             A      67    Answer     116.202.120.166
www.torproject.org                             A      67    Answer     204.8.99.144
www.torproject.org                             A      67    Answer     95.216.163.36
```

🌞 Effectue un scan du réseau auquel tu es connecté

```
PS C:\Users\doria> nmap -sn -PR 10.33.64.0/20
Starting Nmap 7.95 ( https://nmap.org ) at 2024-09-27 16:07 Paris, Madrid (heure dÆÚtÚ)
Nmap scan report for 10.33.66.78
Host is up (0.067s latency).
MAC Address: E4:B3:18:48:36:68 (Intel Corporate)
Nmap scan report for 10.33.67.113
Host is up (0.15s latency).
MAC Address: D2:91:DE:DF:9A:6E (Unknown)
Nmap scan report for 10.33.69.68
Host is up (0.19s latency).
MAC Address: EE:E8:D9:89:3F:F1 (Unknown)
```

🌞 Changer d'adresse IP

```
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::9999:d1b5:dbf0:97b3%23
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.66.80
   Masque de sous-réseau. . . . . . . . . : 255.255.240.0
   Passerelle par défaut. . . . . . . . . : 10.33.79.254
```