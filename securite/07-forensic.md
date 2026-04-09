# Chapitre 07 — Forensic (Investigation numérique)

## Qu'est-ce que le forensic ?

Le **forensic** (ou investigation numérique) consiste à **analyser des preuves numériques** pour comprendre ce qui s'est passé sur un système. Dans les CTF, cela signifie généralement analyser des fichiers, des captures mémoire, des images disque ou des logs pour trouver un flag.

---

## Réflexes généraux face à un fichier inconnu

```bash
# 1. Identifier le type réel du fichier
file challenge.dat

# 2. Extraire les chaînes lisibles
strings challenge.dat
strings -n 8 challenge.dat  # Chaînes d'au moins 8 caractères

# 3. Regarder en hexadécimal
xxd challenge.dat | head -30
hexdump -C challenge.dat | head -30

# 4. Chercher des fichiers embarqués
binwalk challenge.dat
binwalk -e challenge.dat   # Extraire

# 5. Métadonnées
exiftool challenge.dat

# 6. Entropie (fichier chiffré = haute entropie)
binwalk -E challenge.dat
```

---

## Analyse de fichiers

### Fichiers corrompus / à réparer

```bash
# PNG corrompu
pngcheck image.png       # Vérifier l'intégrité
# Réparer manuellement en corrigeant les magic bytes avec un éditeur hex

# ZIP corrompu
zip -T archive.zip       # Tester l'intégrité
zipinfo archive.zip      # Informations sur le contenu
# Réparer
zip --fix archive.zip --out repare.zip

# Voir les fichiers dans une archive (même endommagée)
7z l archive.zip
7z e archive.zip          # Extraire

# Password zip
fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt archive.zip
john --format=zip hash.txt
hashcat -m 17200 hash.txt wordlist.txt
```

### Extraire des hashs pour les cracker
```bash
# ZIP protégé par mot de passe
zip2john archive.zip > hash.txt
john hash.txt --wordlist=rockyou.txt

# PDF protégé
pdf2john fichier.pdf > hash.txt
john hash.txt

# Office (Word, Excel...)
office2john fichier.docx > hash.txt

# RAR
rar2john archive.rar > hash.txt

# SSH clé privée chiffrée
ssh2john id_rsa > hash.txt
```

---

## Analyse de captures réseau (PCAP)

```bash
# Ouvrir avec Wireshark (interface graphique)
wireshark capture.pcap

# Analyser en ligne de commande avec tshark
tshark -r capture.pcap

# Statistiques des protocoles
tshark -r capture.pcap -q -z io,phs

# Extraire des champs spécifiques
tshark -r capture.pcap -T fields -e http.request.uri

# Filtres (même syntaxe que Wireshark)
tshark -r capture.pcap -Y "http"
tshark -r capture.pcap -Y "tcp.port == 80"
tshark -r capture.pcap -Y 'http contains "password"'

# Extraire tous les objets HTTP (fichiers téléchargés)
tshark -r capture.pcap --export-objects http,/tmp/export/

# Extraire les conversations TCP
tshark -r capture.pcap -q -z conv,tcp

# Suivre un stream TCP spécifique
tshark -r capture.pcap -z follow,tcp,ascii,0  # Stream 0
```

### Extraire des données d'un PCAP

```bash
# Avec NetworkMiner (Windows / Mono)
# Reconstitue automatiquement les fichiers transférés

# Avec foremost
foremost -i capture.pcap -o /tmp/sortie/

# Chercher des mots de passe FTP/Telnet/HTTP en clair
strings capture.pcap | grep -i "pass\|user\|login"
tshark -r capture.pcap -Y ftp -T fields -e ftp.request.arg
```

---

## Analyse de mémoire (Memory Forensics)

Les captures mémoire (RAM dumps) permettent de voir ce qui tournait sur un système.

### Volatility — L'outil de référence

```bash
# Identifier le profil (OS et version)
volatility -f memory.dmp imageinfo
volatility -f memory.dmp kdbgscan  # Alternatif

# Avec le bon profil
VOL="volatility -f memory.dmp --profile=Win7SP1x64"

# Lister les processus
$VOL pslist          # Liste simple
$VOL pstree          # Arborescence
$VOL psscan          # Scan (trouve les processus cachés)

# Voir les connexions réseau
$VOL netscan
$VOL connections      # Windows XP

# Lister les fichiers en mémoire
$VOL filescan
$VOL dumpfiles -Q 0x12345678 -D /tmp/  # Extraire un fichier

# Lister les clés de registre
$VOL hivelist         # Lister les ruches
$VOL printkey -K "SAM\Domains\Account\Users"

# Dumps de processus
$VOL memdump -p 1234 -D /tmp/   # Dump d'un processus
strings /tmp/1234.dmp | grep flag

# Rechercher une chaîne dans toute la mémoire
$VOL yarascan -Y "flag{"
$VOL yarascan -Y "ROOT-ME"

# Historique bash / commandes
$VOL linux_bash
$VOL linux_pslist     # Linux

# Volatility 3 (nouvelle version)
python3 vol.py -f memory.dmp windows.pslist
python3 vol.py -f memory.dmp windows.netscan
```

---

## Analyse de disque / Image disque

```bash
# Monter une image disque
sudo mount -o loop,ro disk.img /mnt/image/
sudo umount /mnt/image/

# Avec des partitions
fdisk -l disk.img    # Voir les partitions
losetup -f disk.img  # Créer un loop device
kpartx -a /dev/loop0 # Créer les partitions

# Autopsy — outil forensic graphique
autopsy &

# Recuperation de fichiers supprimés
foremost -i disk.img -o /tmp/recupere/
photorec disk.img    # Interface interactive, très efficace
```

---

## Analyse de logs

```bash
# Logs Linux courants
cat /var/log/auth.log       # Authentifications
cat /var/log/syslog         # Système
cat /var/log/apache2/access.log  # Accès Apache
cat /var/log/apache2/error.log   # Erreurs Apache
cat ~/.bash_history          # Historique des commandes

# Analyser les logs Apache
# Format : IP - - [date] "methode chemin version" code taille
cat access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head 10
# → Top 10 des IPs

cat access.log | grep "404" | awk '{print $7}' | sort | uniq -c
# → Pages introuvables les plus demandées

cat access.log | grep POST | grep -v 200
# → Requêtes POST ayant échoué (tentatives d'injection ?)

# Chercher des signes d'intrusion
grep "UNION SELECT\|../\|<script\|passwd" access.log
```

---

## Forensic de fichiers spécifiques

### Documents Office
```bash
# Extraire le contenu d'un docx/xlsx (ce sont des ZIP)
cp document.docx document.zip
unzip document.zip -d doc_contenu/
cat doc_contenu/word/document.xml

# Chercher des macros
olevba document.doc
olevba document.docx   # python-oletools
oleobj document.doc    # Extraire les objets embarqués
```

### PDF
```bash
# Analyser un PDF
pdfinfo fichier.pdf         # Métadonnées
pdftotext fichier.pdf -     # Extraire le texte
pdfimages fichier.pdf /tmp/images  # Extraire les images

# Analyse forensique d'un PDF
pdf-parser.py fichier.pdf   # peepdf
peepdf fichier.pdf
```

### Bases de données SQLite
```bash
# SQLite est souvent utilisé par les applications (Firefox, Android...)
sqlite3 database.db

sqlite> .tables              # Lister les tables
sqlite> .schema              # Voir la structure
sqlite> SELECT * FROM users; # Requête
sqlite> .quit
```

---

## Timestamps et chronologie

```bash
# Voir les dates de fichiers
stat fichier.txt
ls -la --full-time fichier.txt

# Accès (atime), modification (mtime), changement (ctime)
# Utile pour reconstruire une chronologie d'attaque

# Trouver des fichiers modifiés récemment
find /etc -newer /tmp/reference_file -ls
find / -mtime -1 2>/dev/null   # Modifiés dans les 24h
find / -atime -1 2>/dev/null   # Accédés dans les 24h
```

---

## Challenges Root-Me — Forensic

Challenges recommandés sur [https://www.root-me.org/fr/Challenges/Forensic/](https://www.root-me.org/fr/Challenges/Forensic/) :

### Débutant (5-20 pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **Logs — Analyse de logs** | 15 pts | Analyser des logs Apache |
| **Fichier — Récupération** | 20 pts | Récupérer des fichiers supprimés |
| **Capture réseau 1** | 15 pts | Analyser un PCAP simple |
| **PDF — Analyse** | 20 pts | Analyser un PDF |

### Intermédiaire (25-40 pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **Ransomware** | 35 pts | Analyser un fichier chiffré |
| **Capture réseau — Logs HTTP** | 25 pts | Reconstituer une attaque via les logs |
| **Mémoire — Volatility** | 30 pts | Analyse de mémoire avec Volatility |
| **Android — Forensic** | 35 pts | Analyser une image Android |

### Méthodologie forensic
1. Identifier le type de fichier (`file`, `xxd`)
2. Extraire les données (`strings`, `binwalk`, `exiftool`)
3. Analyser le contenu selon le type (PCAP → Wireshark, mémoire → Volatility)
4. Chercher le flag (`grep -i flag`, `strings | grep ROOT-ME`)

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Root-Me Forensic | [https://www.root-me.org/fr/Challenges/Forensic/](https://www.root-me.org/fr/Challenges/Forensic/) | Challenges forensic |
| CyberDefenders | [https://cyberdefenders.org/](https://cyberdefenders.org/) | Labs forensic (Blue Team) |
| BlueTeamLabs | [https://blueteamlabs.online/](https://blueteamlabs.online/) | Analyse d'incidents |
| Volatility | [https://www.volatilityfoundation.org/](https://www.volatilityfoundation.org/) | Documentation Volatility |

---

[← Stéganographie](06-steganographie.md) | [Retour au README](../README.md) | [Reverse Engineering →](08-reverse.md)
