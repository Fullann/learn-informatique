# Chapitre 02 — Réseau

## Pourquoi comprendre les réseaux ?

Toute communication informatique passe par un réseau. Comprendre comment les données voyagent est indispensable pour :
- Analyser du trafic suspect
- Détecter des intrusions
- Exploiter des services mal configurés

---

## Le modèle OSI — Les 7 couches

```
7 — Application   → HTTP, FTP, DNS, SSH (ce que l'utilisateur voit)
6 — Présentation  → Chiffrement, encodage (SSL/TLS)
5 — Session       → Gestion des connexions
4 — Transport     → TCP, UDP (ports, fiabilité)
3 — Réseau        → IP (adressage, routage)
2 — Liaison       → Ethernet, Wi-Fi (adresses MAC)
1 — Physique      → Câbles, signaux électriques
```

En pratique, on utilise surtout le **modèle TCP/IP** (4 couches) :
```
Application  → HTTP, DNS, FTP, SSH…
Transport    → TCP, UDP
Internet     → IP
Accès réseau → Ethernet, Wi-Fi
```

---

## Adresses IP et masques de sous-réseau

### IPv4
```
Format  : 4 octets séparés par des points
Exemple : 192.168.1.42

Classes :
  10.0.0.0/8       → Réseau privé classe A
  172.16.0.0/12    → Réseau privé classe B
  192.168.0.0/16   → Réseau privé classe C (le plus courant chez soi)
  127.0.0.1        → Loopback (moi-même)
```

### Notation CIDR
```
192.168.1.0/24  → 256 adresses (192.168.1.0 à 192.168.1.255)
192.168.1.0/25  → 128 adresses
10.0.0.0/8      → 16 777 216 adresses
```

---

## TCP vs UDP

### TCP (Transmission Control Protocol)
- **Connexion orientée** (handshake en 3 étapes)
- **Fiable** : garantit la livraison des paquets dans l'ordre
- **Lent** : overhead important
- Utilisé pour : HTTP, HTTPS, SSH, FTP, SMTP

**Handshake TCP (3-way) :**
```
Client  →  SYN          →  Serveur
Client  ←  SYN-ACK      ←  Serveur
Client  →  ACK           →  Serveur
         [Connexion établie]
```

### UDP (User Datagram Protocol)
- **Sans connexion** : envoie et oublie
- **Pas fiable** : paquets peuvent être perdus ou réordonnés
- **Rapide** : peu d'overhead
- Utilisé pour : DNS, DHCP, streaming vidéo, jeux en ligne, VoIP

---

## Les ports

Un port est un numéro (0–65535) qui identifie un service sur une machine.

| Port | Protocole | Service |
|------|-----------|---------|
| 20/21 | TCP | FTP (transfert de fichiers) |
| 22 | TCP | SSH (terminal sécurisé) |
| 23 | TCP | Telnet (terminal non sécurisé) |
| 25 | TCP | SMTP (envoi d'email) |
| 53 | TCP/UDP | DNS (résolution de noms) |
| 80 | TCP | HTTP (web) |
| 110 | TCP | POP3 (réception email) |
| 139/445 | TCP | SMB (partage fichiers Windows) |
| 443 | TCP | HTTPS (web chiffré) |
| 3306 | TCP | MySQL |
| 3389 | TCP | RDP (Bureau à distance Windows) |
| 8080 | TCP | HTTP alternatif |

**Ports importants à retenir :**
- < 1024 = ports privilégiés (root requis pour les ouvrir)
- 1024–49151 = ports enregistrés
- 49152–65535 = ports dynamiques/éphémères

---

## DNS — Domain Name System

Le DNS traduit un nom de domaine en adresse IP.

```
google.com  →  DNS  →  142.250.74.46
```

### Types d'enregistrements DNS
| Type | Rôle |
|------|------|
| **A** | Nom → IPv4 |
| **AAAA** | Nom → IPv6 |
| **MX** | Serveur de mail |
| **CNAME** | Alias vers un autre nom |
| **TXT** | Texte libre (utilisé pour SPF, DKIM…) |
| **NS** | Serveurs DNS autoritaires |
| **PTR** | IP → Nom (reverse DNS) |

```bash
# Interroger le DNS
nslookup google.com
dig google.com
dig google.com MX          # Records MX
dig @8.8.8.8 google.com   # Utiliser DNS Google

# Transfert de zone (souvent mal configuré = fuite d'infos)
dig axfr @ns1.domaine.com domaine.com
```

---

## Nmap — Scanner de ports

`nmap` est l'outil de référence pour découvrir des hôtes et services sur un réseau.

```bash
# Scan basique
nmap 192.168.1.1

# Scanner une plage d'IPs
nmap 192.168.1.0/24

# Scanner des ports spécifiques
nmap -p 80,443,22 192.168.1.1

# Scanner tous les ports
nmap -p- 192.168.1.1

# Scan rapide (ports courants)
nmap -F 192.168.1.1

# Détection de version des services
nmap -sV 192.168.1.1

# Détection du système d'exploitation
nmap -O 192.168.1.1

# Scan complet (version + OS + scripts)
nmap -sV -sC -O 192.168.1.1

# Scan UDP (plus lent)
nmap -sU 192.168.1.1

# Scan furtif (SYN scan)
nmap -sS 192.168.1.1

# Sortie dans un fichier
nmap -oN resultat.txt 192.168.1.1
nmap -oX resultat.xml 192.168.1.1

# Scripts NSE (Nmap Scripting Engine)
nmap --script=http-title 192.168.1.1
nmap --script=vuln 192.168.1.1  # Chercher des vulnérabilités connues
```

---

## Wireshark — Analyser le trafic réseau

**Wireshark** capture et analyse les paquets réseau. C'est l'outil indispensable pour comprendre ce qui circule sur un réseau.

### Filtres utiles Wireshark
```
# Par protocole
http
dns
tcp
udp
icmp
ftp
ssh

# Par adresse IP
ip.addr == 192.168.1.1
ip.src == 192.168.1.1      # Source
ip.dst == 192.168.1.1      # Destination

# Par port
tcp.port == 80
tcp.port == 443

# Combinaisons
http && ip.src == 192.168.1.1
tcp.port == 80 || tcp.port == 443

# Contient une chaîne
http contains "password"
tcp contains "flag"

# Suivre une connexion TCP
# Clic droit sur un paquet → "Follow TCP Stream"
```

### tcpdump — Capturer en ligne de commande
```bash
# Capturer tout le trafic
sudo tcpdump -i eth0

# Capturer vers un fichier
sudo tcpdump -i eth0 -w capture.pcap

# Filtrer par hôte
sudo tcpdump -i eth0 host 192.168.1.1

# Filtrer par port
sudo tcpdump -i eth0 port 80

# Lire un fichier .pcap
tcpdump -r capture.pcap
```

---

## Protocoles courants à connaître

### HTTP / HTTPS
```
Méthodes HTTP : GET, POST, PUT, DELETE, OPTIONS, HEAD
Codes de réponse :
  200 OK         → Succès
  301/302        → Redirection
  400 Bad Request
  401 Unauthorized
  403 Forbidden
  404 Not Found
  500 Internal Server Error
```

### FTP
```bash
# Connexion FTP
ftp 192.168.1.10

# Connexion anonyme (souvent activée par erreur !)
ftp> open 192.168.1.10
ftp> anonymous / (email comme mot de passe)
ftp> ls
ftp> get fichier.txt
```

### SSH
```bash
# Connexion SSH
ssh utilisateur@192.168.1.10
ssh -p 2222 utilisateur@192.168.1.10  # Port personnalisé

# Copier un fichier via SSH
scp fichier.txt user@192.168.1.10:/tmp/

# Tunneling SSH
ssh -L 8080:cible:80 user@pivot  # Port forwarding
```

---

## Attaques réseau courantes (à connaître pour se défendre)

### ARP Spoofing / ARP Poisoning
```
L'ARP (Address Resolution Protocol) traduit une IP en adresse MAC.
Un attaquant sur le réseau local peut envoyer de faux messages ARP
pour rediriger le trafic vers sa machine (Man-in-the-Middle).

Détection : surveiller les tables ARP, utiliser des outils comme arpwatch
```

### Man-in-the-Middle (MitM)
```
L'attaquant s'intercale entre deux parties qui communiquent.
Prévention : utiliser HTTPS, vérifier les certificats, VPN
```

### Scan de ports
```
Pas une attaque en soi, mais une reconnaissance.
Détection : IDS/IPS (Snort, Suricata), fail2ban
```

### Brute Force
```
Tester toutes les combinaisons de mots de passe.
Outils : Hydra, Medusa
Prévention : limitation du taux de tentatives, 2FA, mots de passe forts
```

```bash
# Hydra — brute force SSH
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.10

# Hydra — brute force HTTP form
hydra -l admin -P wordlist.txt 192.168.1.10 http-post-form \
  "/login:user=^USER^&pass=^PASS^:Invalid"
```

---

## Challenges Root-Me — Réseau

Challenges recommandés sur [https://www.root-me.org/fr/Challenges/Reseau/](https://www.root-me.org/fr/Challenges/Reseau/) :

| Challenge | Difficulté | Ce qu'on apprend |
|-----------|-----------|-----------------|
| **FTP — Authentification** | ⭐ 1 pt | Analyser un .pcap FTP, récupérer des identifiants en clair |
| **TELNET — Authentification** | ⭐ 1 pt | Analyser du trafic Telnet non chiffré |
| **ETHERNET — Trame** | ⭐ 1 pt | Comprendre les trames Ethernet |
| **Twitter authentification** | ⭐⭐ 10 pts | Analyser le trafic HTTP d'une authentification |
| **HTTP — Cookies** | ⭐⭐ 10 pts | Manipulation de cookies HTTP |
| **Trafic DNS** | ⭐⭐ 10 pts | Analyser des requêtes DNS |
| **Bluetooth — Fichier inconnu** | ⭐⭐ 10 pts | Analyser un fichier Bluetooth |

### Méthodologie pour les challenges réseau
1. Ouvrir le fichier `.pcap` dans Wireshark
2. Regarder les protocoles présents (Statistics → Protocol Hierarchy)
3. Filtrer par protocole intéressant
4. Chercher des informations sensibles (identifiants, mots de passe, flags)
5. Suivre les streams TCP ou UDP si besoin

---

## Outils réseau utiles

```bash
# netcat — le couteau suisse réseau
nc 192.168.1.10 80          # Se connecter à un port
nc -lvp 4444                 # Écouter sur un port
echo "GET / HTTP/1.0" | nc 192.168.1.10 80  # Requête HTTP manuelle

# curl — requêtes HTTP
curl http://site.com
curl -v http://site.com                          # Verbeux (voir headers)
curl -X POST -d "user=admin&pass=test" http://  # POST
curl -b "session=abc123" http://site.com         # Avec cookie
curl -H "X-Custom: valeur" http://site.com       # Header personnalisé

# traceroute — tracer le chemin des paquets
traceroute google.com
tracert google.com  # Windows
```

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Root-Me Réseau | [https://www.root-me.org/fr/Challenges/Reseau/](https://www.root-me.org/fr/Challenges/Reseau/) | Challenges réseau |
| Wireshark samples | [https://wiki.wireshark.org/SampleCaptures](https://wiki.wireshark.org/SampleCaptures) | Captures réseau d'exemple |
| PacketLife | [https://packetlife.net/captures/](https://packetlife.net/captures/) | Captures réseau |

---

[← Introduction](01-introduction.md) | [Retour au README](../README.md) | [Sécurité Web →](03-web.md)
