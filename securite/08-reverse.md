# Chapitre 08 — Reverse Engineering

## Qu'est-ce que le Reverse Engineering ?

Le **reverse engineering** (ingénierie inverse) consiste à analyser un programme **sans avoir accès à son code source**, pour comprendre son fonctionnement. Dans les CTF, l'objectif est souvent de trouver un mot de passe, un flag, ou de comprendre une logique de vérification.

---

## Les formats de fichiers exécutables

```
ELF (Executable and Linkable Format)
  → Exécutables Linux/Unix
  → Identifiable par : 7F 45 4C 46 (.ELF en magic bytes)
  → file binaire → "ELF 64-bit LSB executable..."

PE (Portable Executable)
  → Exécutables Windows (.exe, .dll)
  → Identifiable par : 4D 5A (MZ en ASCII)

Mach-O
  → Exécutables macOS
```

---

## Analyse statique — Sans exécuter le programme

### Premières commandes à lancer

```bash
# Identifier le type d'exécutable
file binaire

# Chercher des chaînes en clair (mots de passe, flags, URLs, messages...)
strings binaire
strings binaire | grep -i "flag\|password\|root-me\|ctf\|secret"
strings binaire | grep -i "wrong\|correct\|congratul"  # Messages de validation

# Voir les symboles (fonctions, variables)
nm binaire           # Symboles non strippés
nm -D binaire        # Symboles dynamiques (bibliothèques partagées)
readelf -s binaire   # Tous les symboles

# Voir les bibliothèques utilisées
ldd binaire
readelf -d binaire | grep NEEDED

# Informations sur le binaire
readelf -h binaire   # Header ELF
readelf -a binaire   # Tout

# Voir les sections du binaire
objdump -h binaire

# Désassembler (assembly brut)
objdump -d binaire
objdump -d -M intel binaire    # Syntaxe Intel (plus lisible)
objdump -d binaire | grep -A5 "main>"
```

---

## Désassembleurs et décompilateurs

### Ghidra — L'outil indispensable (gratuit)
**Ghidra** est un framework de reverse engineering développé par la NSA, gratuit et très puissant.

```
Télécharger : https://ghidra-sre.org/
Installation : décompresser + ./ghidraRun

Fonctionnalités clés :
  - Désassembleur multiarchitecture (x86, ARM, MIPS...)
  - Décompilateur C (transforme l'assembleur en pseudo-code C lisible)
  - Analyseur de flux de contrôle
  - Renommage de variables et fonctions
  - Recherche de chaînes, fonctions, références croisées
```

**Workflow Ghidra :**
1. Créer un nouveau projet
2. Importer le binaire (File → Import)
3. Analyser (Analysis → Auto Analyze)
4. Chercher `main` dans la liste des fonctions
5. Utiliser le décompilateur (fenêtre de droite) pour lire le pseudo-code C

### Cutter / Radare2
```bash
# Radare2 — outil en ligne de commande
r2 binaire
> aa          # Analyser tout
> afl         # Lister les fonctions
> s main      # Aller à main
> pdf         # Désassembler la fonction actuelle (print disasm function)
> VV          # Vue graphique
> q           # Quitter

# Cutter = interface graphique de Radare2
```

### IDA Pro / IDA Free
```
IDA Pro = référence professionnelle (très cher)
IDA Free = version gratuite limitée aux binaires 64-bit
Télécharger : https://hex-rays.com/ida-free/
```

---

## Analyse dynamique — Exécuter et observer

### strace et ltrace — Tracer les appels système et bibliothèque

```bash
# strace : voir les appels système (fichiers, réseau, processus...)
strace ./binaire
strace -e trace=open,read,write ./binaire  # Filtrer
strace -s 1000 ./binaire                  # Chaînes jusqu'à 1000 chars

# ltrace : voir les appels aux fonctions de bibliothèque
ltrace ./binaire
ltrace -i ./binaire     # Avec adresses

# Très utile pour voir :
# - Les fichiers ouverts
# - Les comparaisons de chaînes (strcmp, strncmp)
# - Les appels réseau
```

Exemple : un programme qui vérifie un mot de passe
```
$ ltrace ./crackme
strcmp("monInput", "secretPassword")  → 0 si correct !
```

### GDB — Le débogueur

**GDB** permet d'exécuter un programme pas à pas, d'inspecter la mémoire et les registres.

```bash
# Lancer GDB
gdb ./binaire
gdb -q ./binaire    # Mode silencieux

# Commandes de base
(gdb) run                  # Exécuter le programme
(gdb) run arg1 arg2        # Avec arguments

(gdb) break main           # Point d'arrêt sur main
(gdb) break *0x4005a0      # Point d'arrêt sur une adresse
(gdb) break fonctionName   # Point d'arrêt sur une fonction

(gdb) continue             # (c) Continuer jusqu'au prochain break
(gdb) next                 # (n) Instruction suivante (sans entrer dans les fonctions)
(gdb) step                 # (s) Instruction suivante (entre dans les fonctions)
(gdb) finish               # Terminer la fonction actuelle

(gdb) info registers       # Voir les registres
(gdb) info registers rax   # Registre spécifique

(gdb) x/10x $rsp           # Afficher 10 mots hex à partir de RSP (stack)
(gdb) x/s 0x4006a0         # Afficher comme chaîne
(gdb) x/10i $rip           # Afficher 10 instructions à partir de RIP

(gdb) print $rax           # Valeur du registre RAX
(gdb) print (char*)$rdi    # Interpréter RDI comme une chaîne

(gdb) disassemble main      # Désassembler main
(gdb) disassemble 0x4005a0  # Désassembler depuis une adresse

(gdb) set $rax = 1         # Modifier un registre
(gdb) quit                 # Quitter
```

### PWNDBG / PEDA — Extensions GDB pour la sécurité

```bash
# Installer pwndbg (recommandé)
git clone https://github.com/pwndbg/pwndbg
cd pwndbg && ./setup.sh

# Ajoute des commandes utiles :
# context  → vue complète (registres, stack, code)
# checksec → voir les protections du binaire
# vmmap    → carte mémoire
```

---

## Protections des binaires

```bash
# Vérifier les protections
checksec --file=binaire

# Protections courantes :
# NX (No eXecute)     → La pile n'est pas exécutable
# ASLR                → Adresses aléatoires à chaque exécution
# PIE                 → Base du binaire aléatoire
# Stack Canary        → Détection des buffer overflows
# RELRO               → Sections en lecture seule
```

---

## L'assembleur x86-64 essentiel

Pour lire du code désassemblé, il faut connaître les bases de l'assembleur.

### Registres principaux (x86-64)
```
RAX, RBX, RCX, RDX  → Registres généraux (calculs, paramètres)
RSI, RDI             → Source et destination (et paramètres 1&2)
RBP                  → Base pointer (bas de la frame de pile)
RSP                  → Stack pointer (sommet de la pile)
RIP                  → Instruction pointer (instruction en cours)
RFLAGS               → Drapeaux (résultats de comparaisons)
```

### Instructions courantes
```asm
mov rax, rbx        ; rax = rbx
mov rax, [rbp-8]    ; rax = valeur à l'adresse rbp-8
lea rax, [rbp-8]    ; rax = adresse rbp-8

push rax            ; Empiler rax sur la pile
pop rax             ; Dépiler dans rax

add rax, 5          ; rax += 5
sub rax, 3          ; rax -= 3
mul rbx             ; rax = rax * rbx
xor rax, rax        ; rax = 0 (pattern courant)

cmp rax, rbx        ; Compare rax et rbx (met les flags)
test rax, rax       ; Test si rax == 0
jmp adresse         ; Saut inconditionnel
je  adresse         ; Saut si égal (Zero Flag = 1)
jne adresse         ; Saut si différent
jl  adresse         ; Saut si inférieur (signé)
jg  adresse         ; Saut si supérieur (signé)

call fonction       ; Appeler une fonction
ret                 ; Retour de fonction

syscall             ; Appel système Linux
```

### Conventions d'appel (System V AMD64)
```
Paramètres passés dans les registres :
  1er paramètre : RDI
  2ème          : RSI
  3ème          : RDX
  4ème          : RCX
  5ème          : R8
  6ème          : R9
  ...reste sur la pile

Valeur de retour : RAX
```

---

## Exemple d'analyse d'un crackme

Voici un exemple typique de challenge reverse :

**Code C du programme (non accessible en CTF) :**
```c
#include <stdio.h>
#include <string.h>

int main() {
    char input[64];
    printf("Mot de passe : ");
    scanf("%63s", input);

    if (strcmp(input, "S3cr3t_P4ssw0rd") == 0) {
        printf("Bravo ! Flag : ROOT-ME{R3v3rs3_1s_3asy}\n");
    } else {
        printf("Mauvais mot de passe.\n");
    }
    return 0;
}
```

**Ce qu'on voit lors de l'analyse :**
```bash
# 1. strings
strings crackme | grep -v "^[^a-zA-Z0-9]"
# → "Mot de passe : "
# → "S3cr3t_P4ssw0rd"    ← trouvé !
# → "Bravo ! Flag : ROOT-ME{R3v3rs3_1s_3asy}"
# → "Mauvais mot de passe."

# 2. ltrace
ltrace ./crackme
# → strcmp("monInput", "S3cr3t_P4ssw0rd")

# 3. Ghidra / décompilateur
# On voit clairement le strcmp dans le pseudo-code C
```

---

## Outils Python pour le reverse

```python
# pwntools — bibliothèque pour CTF
from pwn import *

# Charger un ELF
elf = ELF('./binaire')
print(hex(elf.sym['main']))      # Adresse de main
print(elf.got['strcmp'])         # GOT entry de strcmp

# Interagir avec un processus
p = process('./binaire')
p.sendline(b"input")
print(p.recv())
p.interactive()

# Interagir avec un service réseau
r = remote('challenge.root-me.org', 1234)
r.sendline(b"payload")
r.recvuntil(b"Flag:")
flag = r.recvline()
print(flag)

# Encodage/décodage
p32(0x41424344)      # → b'DCBA' (little-endian 32 bits)
p64(0x41424344)      # → little-endian 64 bits
u32(b'\x44\x43\x42\x41')  # Unpack

# XOR d'une chaîne
data = bytes([0x41, 0x42, 0x43])
key = 0x20
result = bytes([b ^ key for b in data])
```

---

## Techniques courantes dans les CTF Reverse

### Vérification caractère par caractère
```python
# Si le programme vérifie chaque caractère séparément
# On peut utiliser une attaque par oracle de temps ou analyser le code

# Exemple de vulnérabilité (timing attack)
for char in string.printable:
    # Mesurer le temps d'exécution avec ce caractère
    # Le bon caractère prend plus de temps si la vérification est séquentielle
```

### XOR — Déchiffrement courant
```python
chiffre = bytes([0x4E, 0x4F, 0x55, 0x53])
cle = 0x13
clair = bytes([b ^ cle for b in chiffre])
print(clair.decode())  # Si c'est du texte
```

### Anti-debug
```bash
# Certains programmes détectent qu'ils sont dans un débogueur
# Techniques courantes :
# - Vérification de /proc/self/status (TracerPid)
# - Vérification du flag PTRACE
# - Appels à ptrace(PTRACE_TRACEME)
# - Délais ou comportements différents si debuggé

# Contournement dans GDB :
(gdb) handle SIGTRAP noprint nostop pass
# Ou patcher l'instruction avec un NOP
```

---

## Challenges Root-Me — Reverse Engineering

Challenges recommandés sur [https://www.root-me.org/fr/Challenges/Cracking/](https://www.root-me.org/fr/Challenges/Cracking/) :

### Débutant (1-15 pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **ELF x86 — Condition** | 5 pts | Modifier une condition avec `strings` ou GDB |
| **ELF x86 — Mot de passe statique** | 5 pts | Trouver un mot de passe avec `strings` |
| **PE32 — Mot de passe statique** | 5 pts | Binaire Windows |
| **ELF x86 — Mot de passe valide** | 10 pts | Analyse simple avec ltrace |

### Intermédiaire (15-30 pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **ELF x86 — Antidebug** | 20 pts | Contourner une protection anti-debug |
| **ELF ARM — Crackme** | 25 pts | Architecture ARM |
| **Python — Bytecode** | 20 pts | Analyser du bytecode Python (.pyc) |
| **ELF x86 — Chiffrement XOR** | 25 pts | Déchiffrer un XOR |

### Avancé (35+ pts)
| Challenge | Points | Technique |
|-----------|--------|-----------|
| **ELF x64 — Crackme** | 30 pts | Reverse 64 bits avec Ghidra |
| **.NET — Crackme** | 35 pts | Reverse .NET avec dnSpy |
| **ELF x86 — Ptrace** | 40 pts | Bypass anti-debug ptrace |

### Méthodologie pour les challenges reverse
1. `file` → identifier le type (ELF, PE, Python...)
2. `strings` → chercher le flag ou des indices directement
3. `ltrace` / `strace` → observer les appels (souvent suffit !)
4. Ouvrir dans Ghidra → décompilateur C
5. Si besoin, déboguer avec GDB

---

## Outils résumés

| Outil | Usage |
|-------|-------|
| `strings` | Extraire les chaînes en clair |
| `file` | Identifier le type de binaire |
| `ltrace` | Tracer les appels de bibliothèque |
| `strace` | Tracer les appels système |
| `objdump` | Désassembler (ligne de commande) |
| `nm` / `readelf` | Informations sur les symboles |
| **Ghidra** | Décompilateur (principal outil) |
| **GDB** + pwndbg | Débogage dynamique |
| `radare2` / Cutter | Désassembleur/débogueur |
| `pwntools` | Bibliothèque Python pour CTF |

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Root-Me Cracking | [https://www.root-me.org/fr/Challenges/Cracking/](https://www.root-me.org/fr/Challenges/Cracking/) | Challenges reverse |
| Crackmes.one | [https://crackmes.one/](https://crackmes.one/) | Collection de crackmes |
| Ghidra | [https://ghidra-sre.org/](https://ghidra-sre.org/) | Télécharger Ghidra |
| pwn.college | [https://pwn.college/](https://pwn.college/) | Cours reverse/exploitation |
| CS:APP | [https://csapp.cs.cmu.edu/](https://csapp.cs.cmu.edu/) | Livre référence (architecture & exploitation) |

---

[← Forensic](07-forensic.md) | [Retour au README](../README.md)
