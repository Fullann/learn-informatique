# Guide d'apprentissage de l'informatique

Bienvenue dans ce guide complet pour apprendre l'informatique **de zéro**. Ce repo est structuré en parcours progressifs, chacun avec de la théorie, des exemples de code et des liens vers des exercices pratiques.

---

## Parcours d'apprentissage

### Parcours 1 — Le Web
> Idéal pour commencer : visible dans un navigateur, résultats immédiats.

| Chapitre | Sujet |
|----------|-------|
| [01 — HTML](web/01-html.md) | Structure des pages web |
| [02 — CSS](web/02-css.md) | Mise en forme et style |
| [03 — Tailwind CSS](web/03-tailwind.md) | Framework CSS utilitaire |
| [04 — JavaScript](web/04-javascript.md) | Interactivité et logique |
| [05 — jQuery](web/05-jquery.md) | Manipulation du DOM simplifiée |
| [06 — PHP](web/06-php.md) | Programmation côté serveur |

---

### Parcours 2 — C++
> Langage puissant orienté objet, proche du matériel.

| Chapitre | Sujet |
|----------|-------|
| [01 — Introduction](cpp/01-introduction.md) | Installation et premier programme |
| [02 — Variables et types](cpp/02-variables.md) | Types de données, déclarations |
| [03 — Structures de contrôle](cpp/03-controle.md) | if/else, switch, boucles |
| [04 — Fonctions](cpp/04-fonctions.md) | Déclaration, paramètres, retour |
| [05 — Tableaux et pointeurs](cpp/05-tableaux-pointeurs.md) | Mémoire et adresses |
| [06 — Programmation orientée objet](cpp/06-poo.md) | Classes, objets, héritage |
| [07 — STL](cpp/07-stl.md) | Bibliothèque standard (vector, map…) |

---

### Parcours 3 — C
> La base de la base. Comprendre le C, c'est comprendre les ordinateurs.

| Chapitre | Sujet |
|----------|-------|
| [01 — Introduction](c/01-introduction.md) | Compilation et premier programme |
| [02 — Variables et types](c/02-variables.md) | int, float, char… |
| [03 — Structures de contrôle](c/03-controle.md) | Conditions et boucles |
| [04 — Fonctions](c/04-fonctions.md) | Modularité du code |
| [05 — Tableaux et chaînes](c/05-tableaux.md) | Manipulation de données |
| [06 — Pointeurs](c/06-pointeurs.md) | Mémoire, adresses, références |
| [07 — Structures](c/07-structures.md) | struct et types personnalisés |
| [08 — Fichiers](c/08-fichiers.md) | Lecture et écriture de fichiers |

---

### Parcours 4 — Sécurité informatique
> Comprendre comment les systèmes peuvent être attaqués pour mieux les défendre. Orienté CTF et pratique via Root-Me.

| Chapitre | Sujet |
|----------|-------|
| [01 — Introduction](securite/01-introduction.md) | Concepts fondamentaux, éthique, lois |
| [02 — Réseau](securite/02-reseau.md) | TCP/IP, protocoles, sniffing, scans |
| [03 — Sécurité Web](securite/03-web.md) | OWASP, injections SQL, XSS, LFI/RFI |
| [04 — Cryptographie](securite/04-cryptographie.md) | Encodage, chiffrement, hachage |
| [05 — Système & Linux](securite/05-systeme-linux.md) | Permissions, bash, escalade de privilèges |
| [06 — Stéganographie](securite/06-steganographie.md) | Cacher des données dans des fichiers |
| [07 — Forensic](securite/07-forensic.md) | Analyse de fichiers, mémoire, logs |
| [08 — Reverse Engineering](securite/08-reverse.md) | Analyser des binaires, désassemblage |

---

## Comment utiliser ce guide

1. **Lis la théorie** de chaque fichier `.md`
2. **Regarde les exemples** de code fournis
3. **Pratique** sur les sites d'exercices recommandés dans chaque chapitre
4. **Passe au chapitre suivant** quand tu te sens à l'aise

> Conseil : ne saute pas les chapitres. Chaque notion s'appuie sur la précédente.

---

## Ressources générales

| Site | Description |
|------|-------------|
| [MDN Web Docs](https://developer.mozilla.org/fr/) | La référence du web (HTML/CSS/JS) |
| [OpenClassrooms](https://openclassrooms.com/fr/) | Cours en français |
| [W3Schools](https://www.w3schools.com/) | Référence + Try it yourself |
| [freeCodeCamp](https://www.freecodecamp.org/) | Exercices progressifs gratuits |
| [Exercism](https://exercism.org/) | Exercices avec feedback humain |
| [LeetCode](https://leetcode.com/) | Algorithmes et défis de code |
| [Root-Me](https://www.root-me.org/) | Plateforme de challenges sécurité (CTF) |

---

## Questions de révision + idées de mini-projets

### Parcours 1 — Web (HTML/CSS/Tailwind/JavaScript/jQuery/PHP)

**Questions de révision**
1. Quelle différence entre balise bloc et balise inline en HTML ?
2. Quand utiliser `id` et quand utiliser `class` ?
3. Quelle différence entre `margin` et `padding` ?
4. À quoi sert `display: flex` ?
5. Quand préférer Tailwind à du CSS classique ?
6. Quelle différence entre `let`, `const` et `var` en JavaScript ?
7. Qu'est-ce qu'un événement DOM (`click`, `input`, `submit`) ?
8. Quelle différence entre `==` et `===` en JavaScript ?
9. Comment envoyer des données d'un formulaire au serveur en PHP ?
10. Pourquoi valider les entrées utilisateur côté serveur ?

**Idées de mini-projets**
- Page portfolio responsive (HTML/CSS/Tailwind).
- Todo list avec filtres (JavaScript).
- Convertisseur d'unités (JavaScript + DOM).
- Formulaire de contact avec validation (HTML/JS/PHP).
- Mini blog statique puis version dynamique en PHP.

### Parcours 2 — C++

**Questions de révision**
1. Quelle différence entre compilation et exécution ?
2. Quand utiliser `int`, `double`, `char`, `bool` ?
3. Différence entre `for`, `while` et `do...while` ?
4. Pourquoi découper son code en fonctions ?
5. À quoi sert un pointeur ?
6. Différence entre tableau C et `std::vector` ?
7. Qu'est-ce qu'une classe et un objet ?
8. Différence entre `public`, `private` et `protected` ?
9. Quand utiliser une référence (`&`) dans les paramètres ?
10. Pourquoi la STL (`vector`, `map`, `string`) accélère le dev ?

**Idées de mini-projets**
- Calculatrice console (opérations + menu).
- Gestionnaire de notes d'étudiants (`vector`, moyenne, tri).
- Jeu du nombre mystère (boucles, conditions).
- Carnet d'adresses en console (struct/classes + fichiers).
- Mini système de bibliothèque (POO + `map`/`vector`).

### Parcours 3 — C

**Questions de révision**
1. Quel rôle jouent `gcc` et l'édition de liens ?
2. Quelle différence entre déclaration et initialisation ?
3. Que se passe-t-il si une variable n'est pas initialisée ?
4. Pourquoi utiliser des fonctions en C ?
5. Comment manipuler une chaîne de caractères (`char[]`) ?
6. Qu'est-ce qu'un pointeur et l'opérateur `*` / `&` ?
7. Comment passer une variable par adresse à une fonction ?
8. À quoi sert `struct` ?
9. Comment lire/écrire dans un fichier avec `fopen`, `fgets`, `fprintf` ?
10. Quelles erreurs classiques provoquent un segfault ?

**Idées de mini-projets**
- Calculatrice en ligne de commande.
- Gestion de contacts avec `struct`.
- Analyseur de texte (compter mots/lettres/lignes).
- Petit agenda sauvegardé dans un fichier `.txt`.
- Jeu pendu en console (tableaux + conditions + fonctions).

### Parcours 4 — Sécurité informatique

**Questions de révision**
1. Différence entre vulnérabilité, menace et risque ?
2. Pourquoi le modèle OSI aide en analyse d'attaque ?
3. Différence entre encodage, chiffrement et hachage ?
4. Pourquoi HTTPS protège mieux que HTTP ?
5. Citer 3 vulnérabilités web OWASP courantes.
6. À quoi sert un scan Nmap avant un test d'intrusion ?
7. Quand utiliser Wireshark vs tcpdump ?
8. Pourquoi le principe du moindre privilège est crucial sous Linux ?
9. Quel intérêt de la stéganographie en CTF ?
10. Que cherche-t-on en reverse engineering d'un binaire ?

**Idées de mini-projets (légal et local uniquement)**
- Lab réseau local avec VM + scan Nmap documenté.
- Analyse d'un fichier `.pcap` et rapport d'observations.
- Mini checklist d'audit web (auth, headers, entrées).
- Script bash de durcissement Linux de base.
- Write-up CTF (méthodo + outils + solutions).
