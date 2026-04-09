# Chapitre 06 — Stéganographie

## Qu'est-ce que la stéganographie ?

La **stéganographie** est l'art de **cacher des informations à l'intérieur d'autres informations**. Contrairement à la cryptographie (qui rend un message illisible), la stéganographie **cache l'existence même du message**.

Exemples :
- Un flag caché dans les pixels d'une image
- Un texte dissimulé dans un fichier audio
- Un fichier caché dans un autre fichier

---

## Réflexes de base face à un fichier suspect

Avant d'utiliser des outils spécialisés, ces commandes s'appliquent à **tout fichier inconnu** :

```bash
# 1. Identifier le vrai type de fichier (ignore l'extension)
file fichier.png
file challenge.jpg
file mystere

# 2. Extraire les chaînes de caractères lisibles
strings fichier | head -50
strings fichier | grep -i "flag\|root-me\|ctf"

# 3. Regarder en hexadécimal
xxd fichier | head -20
hexdump -C fichier | head -20

# 4. Chercher des fichiers cachés / des données supplémentaires
binwalk fichier.png           # Chercher des fichiers embarqués
binwalk -e fichier.png        # Extraire automatiquement

# 5. Lire les métadonnées
exiftool fichier.jpg
```

---

## Les "magic bytes" — Signatures de fichiers

Chaque format de fichier commence par une **signature hexadécimale** caractéristique. Utile quand le fichier a une mauvaise extension.

| Format | Magic bytes (hex) | ASCII |
|--------|-------------------|-------|
| JPEG | `FF D8 FF` | ÿØÿ |
| PNG | `89 50 4E 47 0D 0A 1A 0A` | .PNG.... |
| GIF | `47 49 46 38` | GIF8 |
| PDF | `25 50 44 46` | %PDF |
| ZIP | `50 4B 03 04` | PK.. |
| RAR | `52 61 72 21` | Rar! |
| 7-Zip | `37 7A BC AF` | 7z.. |
| MP3 | `49 44 33` ou `FF FB` | ID3 |
| ELF | `7F 45 4C 46` | .ELF |
| EXE/PE | `4D 5A` | MZ |

```bash
# Voir les premiers octets d'un fichier
xxd fichier | head -3

# Changer l'extension si le type est connu
file mystere   # "mystere: JPEG image data..."
mv mystere mystere.jpg
```

---

## Stéganographie dans les images

### LSB — Least Significant Bit

La technique la plus courante : modifier le **bit de poids faible** de chaque pixel pour encoder un message. La modification est visuellement imperceptible.

```
Pixel rouge original : 11001010  (202)
Avec bit caché "1"  : 11001011  (203) → différence d'1/255 = invisible
```

#### Outils pour le LSB

```bash
# zsteg — détecter et extraire la stéganographie LSB dans PNG/BMP
zsteg image.png
zsteg -a image.png       # Tester toutes les combinaisons
zsteg -E "b1,r,lsb,xy" image.png  # Extraire un canal spécifique

# steghide — cacher/extraire dans JPEG, BMP, WAV, AU
steghide extract -sf image.jpg            # Extraire (demande mot de passe)
steghide extract -sf image.jpg -p ""      # Mot de passe vide
steghide extract -sf image.jpg -p "password"

# Pour tester plusieurs mots de passe
stegseek image.jpg /usr/share/wordlists/rockyou.txt

# stegsolve (outil graphique) — analyser les plans de bits
# Télécharger : https://github.com/zardus/ctf-tools/blob/master/stegsolve/install
java -jar stegsolve.jar
```

#### Analyse visuelle avec Python
```python
from PIL import Image

img = Image.open("image.png")
pixels = img.load()

# Extraire les bits LSB du canal rouge
bits = ""
for y in range(img.height):
    for x in range(img.width):
        r, g, b = pixels[x, y][:3]
        bits += str(r & 1)  # Bit de poids faible du rouge

# Convertir les bits en texte (par groupes de 8)
chars = [bits[i:i+8] for i in range(0, len(bits), 8)]
message = ''.join(chr(int(c, 2)) for c in chars if int(c, 2) > 0)
print(message[:100])
```

### Métadonnées (EXIF)
Les images contiennent souvent des métadonnées cachées.

```bash
exiftool image.jpg

# Informations utiles à chercher
# - Author, Artist, Comment, Description
# - GPS coordinates
# - Copyright, Software
# - Champs personnalisés

# Extraire toutes les métadonnées
exiftool -all image.jpg
```

### Données cachées après la fin de l'image
```bash
# Les fichiers JPEG se terminent par FF D9
# Tout ce qui suit est ignoré par les lecteurs d'image mais présent dans le fichier

xxd image.jpg | tail -20    # Voir la fin du fichier
binwalk image.jpg            # Détecter des fichiers supplémentaires

# Extraire manuellement (à partir d'un certain offset)
dd if=image.jpg of=extrait.zip bs=1 skip=12345
```

---

## Stéganographie dans l'audio

```bash
# Analyser les métadonnées audio
exiftool audio.mp3
id3v2 -l audio.mp3            # Tags ID3

# Spectrogramme — parfois le message est visible dans le spectre sonore
# Ouvrir dans Audacity (View → Spectrogram) ou Sonic Visualiser

# Détecter et extraire
steghide extract -sf audio.wav

# Données cachées dans les fréquences hautes (inaudibles)
# Ouvrir dans Audacity, sélectionner tout, spectrogram view
```

### Lire un spectrogramme
```
Certains challenges cachent du texte ou une image dans le spectrogramme d'un fichier audio.
Avec Audacity :
  1. Ouvrir le fichier audio
  2. Cliquer sur le nom de la piste → Spectrogram
  3. Zoomer sur les fréquences hautes
  4. Le texte apparaît visuellement
```

---

## Fichiers cachés dans d'autres fichiers

### Concaténation de fichiers
```bash
# Un fichier ZIP peut être caché à la fin d'une image
cat image.png archive.zip > combined.png
# combined.png s'ouvre comme une image normale
# mais contient aussi un ZIP

# Pour extraire
binwalk -e combined.png
# ou
unzip combined.png   # ZIP à la fin
```

### Format ZIP et polyglots
```bash
# Certains fichiers sont valides dans plusieurs formats simultanément
# ex: une image JPEG qui est aussi un ZIP valide

file mystery
binwalk mystery
unzip mystery   # Tenter de l'ouvrir comme un ZIP
```

---

## Texte caché dans du texte

### Espaces de largeur nulle (Zero-Width Characters)
```python
# Certains challenges utilisent des caractères invisibles Unicode
# U+200B (Zero Width Space), U+200C (ZWNJ), U+200D (ZWJ), U+FEFF (BOM)

texte = "Bonjour​‌‍ le monde"  # Il y a des caractères invisibles ici

# Détecter
for i, c in enumerate(texte):
    if ord(c) > 127:
        print(f"Position {i}: U+{ord(c):04X}")

# Outil en ligne : https://330k.github.io/misc_tools/unicode_steganography.html
```

### Acrostiche et variation de casse
```
Parfois, le flag est donné par les initiales de chaque mot
ou par les lettres en majuscules dans un texte.
```

---

## Outils résumés

```bash
# Identification
file fichier          # Type de fichier
xxd fichier | head    # Magic bytes
exiftool fichier      # Métadonnées
strings fichier       # Chaînes lisibles
binwalk fichier       # Signatures embarquées

# Extraction
binwalk -e fichier    # Extraction automatique
steghide extract -sf fichier -p ""  # Steghide (mot de passe vide)
stegseek fichier /usr/share/wordlists/rockyou.txt  # Brute force

# Images
zsteg image.png       # LSB dans PNG
stegsolve             # Analyse visuelle des plans de bits

# Audio
audacity              # Visualisation spectrogramme
sonic-visualiser      # Analyse avancée

# En ligne
# https://stylesuxx.github.io/steganography/ — Encode/décode LSB
# https://aperisolve.fr/                     — Analyse automatique tout-en-un
# https://www.futilitycloset.com/            — Outils variés
```

---

## Challenges Root-Me — Stéganographie

Challenges recommandés sur [https://www.root-me.org/fr/Challenges/Steganographie/](https://www.root-me.org/fr/Challenges/Steganographie/) :

### Débutant (1-15 pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **Fichier — EXIF** | 15 pts | Lire les métadonnées EXIF |
| **Fichier — Texte encodé** | 10 pts | Trouver du texte caché |
| **Twitter — Hashtag** | 15 pts | Analyse de données |
| **Pixel Whiteboard** | 20 pts | LSB dans une image |

### Intermédiaire (20-35 pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **PNG — Plan de bits** | 25 pts | Stegsolve, plans de bits |
| **Son — Spectrogramme** | 25 pts | Visualiser le spectrogramme audio |
| **Steghide** | 20 pts | Extraction avec steghide |
| **LSB — Filigrane** | 30 pts | LSB dans une image |

### Méthodologie pour les challenges stégo
1. `file` → identifier le type réel
2. `strings` → chercher des chaînes lisibles et flags
3. `exiftool` → lire les métadonnées
4. `binwalk` → chercher des fichiers embarqués
5. Ouvrir dans un éditeur hex → regarder les en-têtes et la fin
6. `zsteg` ou `steghide` → tenter l'extraction
7. Aperisolve → analyse automatique en ligne

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Root-Me Stéganographie | [https://www.root-me.org/fr/Challenges/Steganographie/](https://www.root-me.org/fr/Challenges/Steganographie/) | Challenges stégo |
| Aperisolve | [https://www.aperisolve.fr/](https://www.aperisolve.fr/) | Analyse automatique d'images |
| StegOnline | [https://stegonline.georgeom.net/upload](https://stegonline.georgeom.net/upload) | Analyse et extraction LSB |
| Forensically | [https://29a.ch/photo-forensics/](https://29a.ch/photo-forensics/) | Analyse forensique d'images |

---

[← Système & Linux](05-systeme-linux.md) | [Retour au README](../README.md) | [Forensic →](07-forensic.md)
