# Chapitre 04 — Cryptographie

## Qu'est-ce que la cryptographie ?

La **cryptographie** est l'art de protéger des informations en les transformant pour les rendre illisibles à quiconque ne possède pas la clé de déchiffrement.

En sécurité informatique, on rencontre souvent de la crypto dans les challenges CTF. L'objectif est de **reconnaître l'encodage ou le chiffrement** utilisé et de le **reverser** pour obtenir le message original (le flag).

---

## Encodage vs Chiffrement vs Hachage

| | Encodage | Chiffrement | Hachage |
|---|---------|-------------|---------|
| **Clé ?** | Non | Oui | Non |
| **Réversible ?** | Oui (direct) | Oui (avec clé) | Non (sens unique) |
| **But** | Représenter des données | Confidentialité | Intégrité, mots de passe |
| **Exemples** | Base64, Hex, URL | AES, RSA, Caesar | MD5, SHA1, SHA256 |

---

## Encodages courants

### Base64
Encode n'importe quelles données en caractères ASCII imprimables (A-Z, a-z, 0-9, +, /).
Reconnaissable au `=` ou `==` en fin de chaîne.

```
Clair  : "Bonjour"
Base64 : "Qm9uam91cg=="
```

```bash
# Décoder
echo "Qm9uam91cg==" | base64 -d

# Encoder
echo "Bonjour" | base64

# Python
python3 -c "import base64; print(base64.b64decode('Qm9uam91cg==').decode())"
```

### Base32
Similaire à Base64 mais avec l'alphabet A-Z et 2-7. Se termine par `=`.
```
Clair  : "Bonjour"
Base32 : "IJQWK3TPNY======"
```

```bash
echo "IJQWK3TPNY======" | base32 -d
python3 -c "import base64; print(base64.b32decode('IJQWK3TPNY======').decode())"
```

### Hexadécimal
Représente chaque octet par 2 chiffres hexadécimaux (0-9, a-f).
```
Clair : "Bonjour"
Hex   : 426f6e6a6f7572
```

```bash
# Décoder
echo "426f6e6a6f7572" | xxd -r -p
python3 -c "print(bytes.fromhex('426f6e6a6f7572').decode())"

# Encoder
echo -n "Bonjour" | xxd -p
```

### URL encoding
Encode les caractères spéciaux en `%XX`.
```
"Bonjour le monde" → "Bonjour%20le%20monde"
"<script>"         → "%3Cscript%3E"
```

```python
import urllib.parse
urllib.parse.unquote("Bonjour%20le%20monde")   # Décoder
urllib.parse.quote("Bonjour le monde")          # Encoder
```

### ROT13
Décalage de 13 lettres dans l'alphabet (chiffre lui-même).
```
Clair  : "Bonjour"
ROT13  : "Obawbhe"
```

```bash
echo "Obawbhe" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
python3 -c "import codecs; print(codecs.decode('Obawbhe', 'rot_13'))"
```

### ASCII
Chaque caractère est représenté par son code décimal (ou hex).
```
B=66, o=111, n=110, j=106, o=111, u=117, r=114
```

```python
# Décoder une liste de codes ASCII
codes = [66, 111, 110, 106, 111, 117, 114]
print(''.join(chr(c) for c in codes))
```

### Binaire
Représentation en bits.
```
"A" = 01000001
"B" = 01000010
```

```python
bits = "01000001 01000010"
print(''.join(chr(int(b, 2)) for b in bits.split()))
```

---

## Chiffrements classiques (historiques)

### Chiffre de César
Décale chaque lettre d'un certain nombre de positions dans l'alphabet.
```
Clé = 3
A→D, B→E, C→F, ...
"Bonjour" → "Erqmrxu"
```

```python
def caesar_decode(texte, decalage):
    result = ""
    for c in texte:
        if c.isalpha():
            base = ord('A') if c.isupper() else ord('a')
            result += chr((ord(c) - base - decalage) % 26 + base)
        else:
            result += c
    return result

# Brute force (tester les 26 décalages)
for i in range(26):
    print(f"Décalage {i}: {caesar_decode('Erqmrxu', i)}")
```

### Chiffre de Vigenère
Comme César mais avec une clé de plusieurs lettres.
```
Clair : BONJOUR
Clé   : CLE
        CLECLEC
Chiffré : DRPNQZV
```

```python
def vigenere_decode(texte, cle):
    result = ""
    cle = cle.upper()
    j = 0
    for c in texte:
        if c.isalpha():
            shift = ord(cle[j % len(cle)]) - ord('A')
            base = ord('A') if c.isupper() else ord('a')
            result += chr((ord(c.upper()) - ord('A') - shift) % 26 + ord('A'))
            j += 1
        else:
            result += c
    return result

print(vigenere_decode("DRPNQZV", "CLE"))  # BONJOUR
```

### Chiffre de substitution
Chaque lettre est remplacée par une autre (table de substitution).
```
A→Q, B→W, C→E, D→R, ...
Analyse de fréquences pour le casser (E est la lettre la plus fréquente en français)
```

### Morse
```
... --- ...  =  SOS
A = .-    E = .    T = -
B = -...  F = ..-.  U = ..-
```

```python
# Décodeur morse
morse = {
    '.-': 'A', '-...': 'B', '-.-.': 'C', '-..': 'D', '.': 'E',
    '..-.': 'F', '--.': 'G', '....': 'H', '..': 'I', '.---': 'J',
    '-.-': 'K', '.-..': 'L', '--': 'M', '-.': 'N', '---': 'O',
    '.--.': 'P', '--.-': 'Q', '.-.': 'R', '...': 'S', '-': 'T',
    '..-': 'U', '...-': 'V', '.--': 'W', '-..-': 'X', '-.--': 'Y',
    '--..': 'Z'
}
message = "... --- ..."
print(''.join(morse.get(m, '?') for m in message.split()))  # SOS
```

---

## Hachage

Le hachage produit une **empreinte fixe** d'un message. Il est **irréversible** mais on peut le casser par **dictionnaire** ou **brute force**.

```
MD5     : 128 bits — ex: 5d41402abc4b2a76b9719d911017c592
SHA1    : 160 bits — ex: aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d
SHA256  : 256 bits — ex: 2c624232cdd221771294dfbb310acbc...
bcrypt  : Adaptatif (salé) — utilisé pour les mots de passe
```

```bash
# Calculer des hashs
echo -n "hello" | md5sum
echo -n "hello" | sha1sum
echo -n "hello" | sha256sum

# Identifier un hash inconnu
hash-identifier "5d41402abc4b2a76b9719d911017c592"
# Ou en ligne : https://www.tunnelsup.com/hash-analyzer/
```

### Casser des hashs avec John the Ripper
```bash
# Cracker un hash MD5 avec un dictionnaire
echo "5d41402abc4b2a76b9719d911017c592" > hash.txt
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# Cracker un hash SHA1
john --format=raw-sha1 --wordlist=rockyou.txt hash.txt

# Voir le résultat
john --show hash.txt
```

### Casser des hashs avec Hashcat
```bash
# Identifier le type de hash (mode)
hashcat --example-hashes | grep MD5

# MD5 brute force
hashcat -m 0 -a 3 5d41402abc4b2a76b9719d911017c592 ?a?a?a?a?a

# MD5 avec dictionnaire
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt

# SHA1 avec dictionnaire
hashcat -m 100 -a 0 hash.txt rockyou.txt
```

### Casser des hashs en ligne
- [https://crackstation.net/](https://crackstation.net/)
- [https://hashes.com/en/decrypt/hash](https://hashes.com/en/decrypt/hash)
- [https://md5decrypt.net/](https://md5decrypt.net/)

---

## Chiffrement moderne

### Chiffrement symétrique (AES)
Une seule clé pour chiffrer et déchiffrer.
```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

key = b'0123456789abcdef'  # 16 bytes pour AES-128
iv  = b'abcdef0123456789'  # Vecteur d'initialisation

# Chiffrer
cipher = AES.new(key, AES.MODE_CBC, iv)
chiffre = cipher.encrypt(pad(b"Bonjour !", 16))

# Déchiffrer
cipher2 = AES.new(key, AES.MODE_CBC, iv)
clair = unpad(cipher2.decrypt(chiffre), 16)
print(clair.decode())
```

### Chiffrement asymétrique (RSA)
Deux clés : **publique** (pour chiffrer) et **privée** (pour déchiffrer).
```
n = p × q       (p et q sont deux grands nombres premiers)
e = exposant public (souvent 65537)
d = exposant privé
Chiffrement : c = m^e mod n
Déchiffrement : m = c^d mod n
```

```python
# RSA simple en Python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP

# Générer une paire de clés
key = RSA.generate(2048)
private_key = key.export_key()
public_key = key.publickey().export_key()

# Chiffrer avec la clé publique
cipher = PKCS1_OAEP.new(RSA.import_key(public_key))
chiffre = cipher.encrypt(b"Message secret")

# Déchiffrer avec la clé privée
cipher2 = PKCS1_OAEP.new(RSA.import_key(private_key))
clair = cipher2.decrypt(chiffre)
print(clair.decode())  # "Message secret"
```

### Attaque RSA — n petit / factorisation
```python
# Si n est petit, on peut le factoriser pour retrouver p et q
# Outil : factordb.com

from sympy import factorint, mod_inverse

n = 3233
e = 17
c = 2790  # Message chiffré

# Factoriser n
factors = factorint(n)  # {61: 1, 53: 1}
p, q = list(factors.keys())

phi = (p - 1) * (q - 1)
d = mod_inverse(e, phi)     # Clé privée
m = pow(c, d, n)             # Déchiffrement
print(m)  # Message en clair
```

---

## Identifier rapidement un encodage

```
Contient = et lettres/chiffres → Base64
Contient = et lettres MAJ uniquement → Base32
Que des 0-9a-f → Hexadécimal
Que des . et - → Morse
Que des 0 et 1 → Binaire
%XX → URL encoding
Seulement des lettres décalées → César ou Vigenère
32 chars hex → MD5
40 chars hex → SHA1
64 chars hex → SHA256
$2y$ ou $2b$ → bcrypt
```

### CyberChef — L'outil magique
**CyberChef** est un outil web qui permet d'appliquer des dizaines de transformations en chaîne.
[https://gchq.github.io/CyberChef/](https://gchq.github.io/CyberChef/)

La fonctionnalité **"Magic"** essaie automatiquement différents décodages.

---

## Challenges Root-Me — Cryptographie

Challenges recommandés sur [https://www.root-me.org/fr/Challenges/Cryptanalyse/](https://www.root-me.org/fr/Challenges/Cryptanalyse/) :

### Débutant (1-10 pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **Encodage — ASCII** | 5 pts | Convertir des codes ASCII |
| **Encodage — UU** | 5 pts | Décoder UUencoding |
| **Hash — Message Digest 5** | 10 pts | Identifier et casser un MD5 |
| **Hash — SHA-2** | 10 pts | Hash SHA |
| **Chiffrement par décalage** | 10 pts | Chiffre de César |

### Intermédiaire (15-30 pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **Encodage — Base64** | 10 pts | Décodage Base64 |
| **Chiffrement par substitution** | 15 pts | Analyse de fréquences |
| **Vigenère — Chiffrement** | 20 pts | Kasiski, Friedman |
| **Hash — LM** | 20 pts | LM Hash Windows |
| **RSA — Facteur commun** | 30 pts | Factorisation de n |

### Avancé (40+ pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **Padding Oracle** | 50 pts | Attaque sur AES-CBC |
| **RSA — Wiener** | 50 pts | Attaque de Wiener sur d petit |
| **ECDSA — Faible nonce** | 60 pts | Cryptographie sur courbes elliptiques |

---

## Quiz de fin de chapitre (25 questions)

1. Quelle est la différence principale entre encodage et chiffrement ?
2. Le hachage est-il réversible ? Pourquoi ?
3. Quel indice visuel suggère souvent du Base64 ?
4. Quelle commande shell permet de décoder du Base64 ?
5. En quoi Base32 se distingue-t-il de Base64 ?
6. Quelle est la représentation hexadécimale de "AB" ?
7. Que signifie `%20` en URL encoding ?
8. ROT13 est-il un chiffrement fort ? Pourquoi ?
9. Quelle propriété de sécurité vise un hash (MD5/SHA256) ?
10. Pourquoi MD5 n'est-il plus recommandé pour la sécurité ?
11. Quelle est la longueur typique d'un hash SHA1 en hexadécimal ?
12. Quelle est la longueur typique d'un hash SHA256 en hexadécimal ?
13. Dans César, que signifie "brute force 26" ?
14. Quel est le principe de Vigenère par rapport à César ?
15. Quelle attaque classique casse une substitution monoalphabétique ?
16. Quelle est la différence clé entre AES et RSA ?
17. En chiffrement asymétrique, quelle clé sert à chiffrer un message à destination d'Alice ?
18. Pourquoi RSA avec un petit `n` est dangereux ?
19. À quoi sert `iv` en AES-CBC ?
20. Quelle est la différence entre confidentialité et intégrité ?
21. Quel outil web est pratique pour enchaîner encodages/décodages ?
22. Si une chaîne est uniquement composée de `0` et `1`, quelle piste tester d'abord ?
23. À quoi servent `john --show` ou `hashcat --show` après un crack ?
24. Pourquoi bcrypt est-il plus adapté que SHA256 seul pour les mots de passe ?
25. Dans un CTF crypto, quelle première étape réduit le plus le temps de résolution ?

### Correction rapide
1. Encodage = représentation, chiffrement = confidentialité avec clé.
2. Non, c'est à sens unique.
3. Présence de `=` en fin et alphabet Base64.
4. `echo "..." | base64 -d`
5. Alphabet A-Z + 2-7, padding `=`.
6. `4142`
7. Un espace.
8. Non, transformation triviale et réversible sans clé secrète.
9. Intégrité (et vérification d'empreinte).
10. Collisions connues.
11. 40 caractères hex.
12. 64 caractères hex.
13. Tester tous les décalages possibles.
14. Décalage variable dépendant d'une clé.
15. Analyse de fréquences.
16. AES symétrique, RSA asymétrique.
17. La clé publique d'Alice.
18. Factorisation plus facile.
19. Randomiser le premier bloc et éviter des motifs répétitifs.
20. Confidentialité cache le contenu, intégrité détecte la modification.
21. CyberChef.
22. Binaire (ASCII binaire).
23. Afficher le mot de passe trouvé.
24. bcrypt est salé et coûteux à calculer (anti brute force).
25. Identifier la famille (encodage/chiffrement/hash) avant d'attaquer.

---

## Mini-labs pratiques (avec correction attendue)

> Objectif : résoudre sans outil "magique" d'abord, puis vérifier avec CyberChef.

### Lab 1 — Identifier puis décoder
**Entrée :**
`U2VjdXJpdGVfQ1RGX0Jhc2U2NA==`

**À faire :**
1. Identifier le format.
2. Décoder la chaîne.

**Correction attendue :**
- Type : Base64
- Message clair : `Securite_CTF_Base64`

### Lab 2 — Hex vers texte
**Entrée :**
`666c61677b6865785f69735f66756e7d`

**À faire :**
1. Identifier le format.
2. Convertir en ASCII.

**Correction attendue :**
- Type : Hexadécimal
- Texte : `flag{hex_is_fun}`

### Lab 3 — César
**Entrée :**
`iodj{fdhvdu_lv_hdvb}`

**À faire :**
1. Tester les 26 décalages.
2. Trouver le message lisible.

**Correction attendue :**
- Décalage : 3
- Texte : `flag{caesar_is_easy}`

### Lab 4 — Hash simple
**Entrée :**
`5f4dcc3b5aa765d61d8327deb882cf99`

**À faire :**
1. Identifier le type de hash probable.
2. Retrouver le mot de passe avec dictionnaire.

**Correction attendue :**
- Type : MD5
- Mot de passe : `password`

### Lab 5 — RSA jouet
**Entrée :**
`n=3233, e=17, c=2790`

**À faire :**
1. Factoriser `n`.
2. Calculer `phi`, puis `d`.
3. Déchiffrer `c`.

**Correction attendue :**
- `p=61`, `q=53`
- `phi=3120`
- `d=2753`
- Message numérique : `65` (caractère ASCII `A`)

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Root-Me Cryptanalyse | [https://www.root-me.org/fr/Challenges/Cryptanalyse/](https://www.root-me.org/fr/Challenges/Cryptanalyse/) | Challenges crypto |
| CyberChef | [https://gchq.github.io/CyberChef/](https://gchq.github.io/CyberChef/) | Encodage/décodage en ligne |
| CrackStation | [https://crackstation.net/](https://crackstation.net/) | Cassage de hashs en ligne |
| dCode.fr | [https://www.dcode.fr/](https://www.dcode.fr/) | Outils de cryptanalyse |
| FactorDB | [http://factordb.com/](http://factordb.com/) | Base de factorisations |

---

[← Sécurité Web](03-web.md) | [Retour au README](../README.md) | [Système & Linux →](05-systeme-linux.md)
