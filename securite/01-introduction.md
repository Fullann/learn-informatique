# Chapitre 01 — Sécurité informatique : Introduction

## Qu'est-ce que la sécurité informatique ?

La **sécurité informatique** (ou cybersécurité) regroupe l'ensemble des techniques, pratiques et outils visant à **protéger les systèmes, réseaux et données** contre les accès non autorisés, les attaques et les dommages.

Il y a deux grandes approches complémentaires :
- **Défensive (Blue Team)** : protéger, surveiller, répondre aux incidents
- **Offensive (Red Team)** : tester les défenses en simulant des attaques

Apprendre à attaquer, c'est apprendre à défendre.

---

## Le cadre légal — Ce qu'il faut savoir

> **IMPORTANT : En France, le Code pénal punit sévèrement toute intrusion informatique non autorisée.**

### Loi Godfrain (art. 323-1 du Code pénal)
- Accéder frauduleusement à un système informatique : **jusqu'à 2 ans de prison et 60 000 € d'amende**
- Si cela entraîne une modification ou destruction : **jusqu'à 3 ans et 100 000 €**
- Si le système est "essentiel" (État, hôpital…) : **jusqu'à 5 ans et 150 000 €**

### La règle d'or
> Tu n'as le droit d'attaquer **que ce que tu possèdes** ou ce pour quoi tu as une **autorisation écrite explicite**.

### Ce qui est légal et recommandé pour apprendre
- Les plateformes CTF (**Root-Me**, HackTheBox, TryHackMe, PicoCTF…)
- Tes propres machines virtuelles (VMs)
- Les labs dédiés (DVWA, VulnHub…)
- Les bug bounty programs (HackerOne, Bugcrowd) avec les règles de chaque programme

---

## Les domaines de la sécurité

| Domaine | Description |
|---------|-------------|
| **Réseau** | Analyse du trafic, scans, protocoles |
| **Web** | Injections, XSS, authentification faible |
| **Cryptographie** | Cassage de chiffrements, encodages |
| **Système/Linux** | Permissions, SUID, escalade de privilèges |
| **Stéganographie** | Données cachées dans des images, sons |
| **Forensic** | Analyse post-incident, récupération de données |
| **Reverse Engineering** | Analyser un binaire sans le code source |
| **Exploitation** | Buffer overflow, format strings |

---

## Les CTF — Capture The Flag

Un **CTF** est une compétition de sécurité où tu dois trouver des "drapeaux" (flags) cachés dans des challenges. C'est le meilleur moyen d'apprendre de façon pratique et légale.

Format typique d'un flag : `ROOT-ME{m0n_pr3m13r_fl4g}` ou `CTF{...}`

### Types de challenges
- **Web** : exploiter une application web vulnérable
- **Crypto** : décoder/déchiffrer un message
- **Stego** : trouver des données cachées
- **Forensic** : analyser un fichier ou une capture réseau
- **Reverse** : analyser un binaire pour trouver le mot de passe
- **Pwn/Exploit** : exploiter une vulnérabilité mémoire

---

## Root-Me — La plateforme recommandée

**Root-Me** (https://www.root-me.org/) est une plateforme française de challenges de sécurité. Elle est parfaite pour les débutants car :
- Entièrement gratuite (premium optionnel)
- Interface en français
- Classée par difficulté
- Des milliers de challenges dans tous les domaines
- Une communauté active

### Comment commencer sur Root-Me
1. Créer un compte sur [https://www.root-me.org/](https://www.root-me.org/)
2. Aller dans **Challenges**
3. Commencer par les challenges notés **1 point** (les plus faciles)
4. Lire les **statements** (énoncés) attentivement
5. Utiliser les **forums** de chaque challenge après avoir résolu (pour voir d'autres approches)

---

## Environnement de travail recommandé

### Kali Linux — La distribution de référence
Kali Linux est une distribution Linux pensée pour la sécurité, avec des centaines d'outils préinstallés.

- **En machine virtuelle** (recommandé pour débuter) : [https://www.kali.org/get-kali/#kali-virtual-machines](https://www.kali.org/get-kali/#kali-virtual-machines)
- **En dual boot**
- **En WSL2** sur Windows

### VirtualBox ou VMware
Pour créer des machines virtuelles :
- VirtualBox (gratuit) : [https://www.virtualbox.org/](https://www.virtualbox.org/)
- VMware Workstation Player (gratuit) : [https://www.vmware.com/](https://www.vmware.com/)

### Outils essentiels (tous dans Kali)
| Outil | Usage |
|-------|-------|
| `nmap` | Scanner de ports réseau |
| `Wireshark` | Analyser le trafic réseau |
| `Burp Suite` | Proxy pour tester les apps web |
| `John the Ripper` | Cracker des hashs |
| `Hashcat` | Cracker des hashs (GPU) |
| `Gobuster` / `ffuf` | Brute-force de répertoires web |
| `strings` | Extraire les chaînes d'un binaire |
| `file` | Identifier le type d'un fichier |
| `xxd` / `hexdump` | Lire un fichier en hexadécimal |
| `binwalk` | Extraire des données de fichiers |
| `steghide` | Stéganographie |
| `exiftool` | Lire les métadonnées |

---

## Les concepts fondamentaux

### La triade CIA
```
Confidentialité  — Les données ne sont accessibles qu'aux personnes autorisées
Intégrité        — Les données n'ont pas été modifiées de façon non autorisée
Disponibilité    — Les systèmes sont accessibles quand on en a besoin
```

### Types d'attaquants
| Terme | Description |
|-------|-------------|
| **White hat** | Hacker éthique, travaille pour défendre |
| **Black hat** | Hacker malveillant |
| **Grey hat** | Entre les deux, trouve des failles sans autorisation mais sans intention malveillante |
| **Script kiddie** | Utilise des outils sans comprendre ce qu'il fait |
| **Pentester** | Professionnel mandaté pour tester la sécurité |

### Vocabulaire clé
| Terme | Définition |
|-------|------------|
| **Vulnérabilité** | Faille dans un système |
| **Exploit** | Code qui tire parti d'une vulnérabilité |
| **Payload** | La charge utile (le code malveillant) |
| **CVE** | Identifiant d'une vulnérabilité connue (ex: CVE-2021-44228) |
| **0-day** | Vulnérabilité non encore connue/patchée |
| **Pentest** | Test d'intrusion autorisé |
| **OSINT** | Renseignement via sources ouvertes (Internet) |
| **Social engineering** | Manipuler des humains plutôt que des systèmes |

---

## Commandes Linux essentielles pour la sécurité

La plupart des outils de sécurité tournent sous Linux. Voici les commandes de base indispensables.

```bash
# Navigation
ls -la          # Lister les fichiers (avec cachés et permissions)
cd /chemin      # Changer de répertoire
pwd             # Afficher le répertoire courant
find / -name "flag.txt" 2>/dev/null  # Chercher un fichier

# Fichiers
cat fichier.txt           # Afficher un fichier
file mystere              # Identifier le type d'un fichier
strings binaire           # Extraire les chaînes lisibles
xxd fichier | head        # Voir le contenu en hexadécimal
hexdump -C fichier        # Dump hexadécimal formaté

# Réseau
ifconfig / ip a           # Voir ses interfaces réseau
ping 192.168.1.1          # Tester la connectivité
curl http://site.com      # Faire une requête HTTP
wget http://site.com/f    # Télécharger un fichier
netstat -tulpn            # Voir les ports ouverts

# Permissions
ls -la                    # Voir les permissions
chmod +x script.sh        # Rendre exécutable
id                        # Voir son utilisateur et ses groupes
whoami                    # Voir son nom d'utilisateur
sudo -l                   # Voir ce qu'on peut faire en tant que root

# Processus
ps aux                    # Lister les processus
top / htop                # Moniteur système

# Pipe et redirections
commande | grep "motif"   # Filtrer la sortie
commande > fichier.txt    # Rediriger vers un fichier
commande 2>/dev/null      # Ignorer les erreurs
```

---

## Méthodologie d'un pentest / CTF

```
1. RECONNAISSANCE (Recon)
   └── Qu'est-ce qui est en face ? Quels services ? Quelle version ?

2. SCANNING / ÉNUMÉRATION
   └── Quels ports sont ouverts ? Quels répertoires web ? Quels utilisateurs ?

3. EXPLOITATION
   └── Y a-t-il une vulnérabilité exploitable ?

4. POST-EXPLOITATION
   └── Escalade de privilèges, persistance, pivot réseau

5. RAPPORT
   └── Documenter les failles trouvées et les recommandations
```

---

## Premiers pas sur Root-Me

Voici les challenges recommandés pour bien commencer, classés par ordre de difficulté.

### Pour démarrer (HTML/Web basique)
- **HTML — Code source** *(1 pt)* — Inspecter le code source d'une page
- **HTTP — Open Redirect** *(10 pts)* — Manipulation d'URL

### Cryptographie basique
- **Encodage — ASCII** *(5 pts)* — Convertir des codes ASCII
- **Encodage — UU** *(5 pts)* — Décoder un format uuencoding

### Stéganographie
- **Fichier — EXIF** *(15 pts)* — Lire les métadonnées d'une image

---

## Sites pour apprendre et pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Root-Me | [https://www.root-me.org/](https://www.root-me.org/) | Plateforme CTF française, recommandée |
| TryHackMe | [https://tryhackme.com/](https://tryhackme.com/) | Apprentissage guidé, très accessible |
| HackTheBox | [https://www.hackthebox.com/](https://www.hackthebox.com/) | Plus avancé, machines réelles |
| PicoCTF | [https://picoctf.org/](https://picoctf.org/) | CTF éducatif (niveau lycée/université) |
| CTFlearn | [https://ctflearn.com/](https://ctflearn.com/) | Challenges accessibles |
| OverTheWire | [https://overthewire.org/wargames/](https://overthewire.org/wargames/) | Wargames Linux (Bandit pour débuter) |
| OWASP | [https://owasp.org/](https://owasp.org/) | Référence sécurité web |

---

[← Retour au README](../README.md) | [Chapitre suivant : Réseau →](02-reseau.md)
