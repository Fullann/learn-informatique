# Chapitre 01 — C : Introduction

## Qu'est-ce que le langage C ?

Le **C** est un langage de programmation créé par Dennis Ritchie en 1972 aux laboratoires Bell. C'est l'un des langages les plus influents de l'histoire de l'informatique :

- Le **noyau Linux**, macOS, Windows sont écrits en C
- La plupart des langages modernes (C++, Java, Python, PHP) en sont inspirés
- Extrêmement **rapide** et proche du matériel
- Utilisé dans l'embarqué, les microcontrôleurs, les pilotes

Apprendre le C, c'est comprendre comment fonctionne réellement un ordinateur.

---

## Différences C et C++

| C | C++ |
|---|-----|
| Procédural | Procédural + Orienté Objet |
| Pas de classes | Classes, héritage, polymorphisme |
| `printf` / `scanf` | `cout` / `cin` |
| `malloc` / `free` | `new` / `delete` (et smart pointers) |
| Pas de `string` | `std::string` |
| `.c` | `.cpp` |

---

## Installation

### Windows
- **MinGW** : [https://winlibs.com/](https://winlibs.com/)
- Ou **Visual Studio** avec le workload C/C++

### macOS
```bash
xcode-select --install  # Installe gcc et clang
```

### Linux
```bash
sudo apt install gcc build-essential
```

### Vérifier
```bash
gcc --version
```

---

## Compiler et exécuter

```bash
# Compiler
gcc main.c -o programme

# Exécuter
./programme

# Avec options recommandées
gcc -std=c11 -Wall -Wextra -o programme main.c
```

---

## Premier programme

```c
#include <stdio.h>   /* Bibliothèque d'entrée/sortie */

int main() {
    printf("Bonjour le monde !\n");
    return 0;
}
```

### Décorticage

| Élément | Signification |
|---------|---------------|
| `#include <stdio.h>` | Inclure la bibliothèque Standard I/O |
| `int main()` | Point d'entrée obligatoire |
| `printf()` | Afficher du texte |
| `\n` | Retour à la ligne |
| `return 0;` | Indiquer que tout s'est bien passé |

---

## printf — Afficher des données

`printf` utilise des **formats** pour insérer des valeurs dans le texte.

```c
#include <stdio.h>

int main() {
    int age = 25;
    float taille = 1.75;
    char initiale = 'A';
    char prenom[] = "Alice";

    printf("Prénom : %s\n", prenom);
    printf("Âge : %d ans\n", age);
    printf("Taille : %.2f m\n", taille);
    printf("Initiale : %c\n", initiale);
    printf("%s a %d ans et mesure %.2f m\n", prenom, age, taille);

    return 0;
}
```

| Format | Type |
|--------|------|
| `%d` | int |
| `%f` | float / double |
| `%.2f` | float avec 2 décimales |
| `%c` | char |
| `%s` | chaîne (char[]) |
| `%ld` | long int |
| `%u` | unsigned int |
| `%p` | pointeur (adresse) |

---

## scanf — Lire des données

```c
#include <stdio.h>

int main() {
    char prenom[50];
    int age;

    printf("Quel est ton prénom ? ");
    scanf("%s", prenom);  // Lit jusqu'à l'espace

    printf("Quel est ton âge ? ");
    scanf("%d", &age);    // Note le & pour les variables numériques

    printf("Bonjour %s, tu as %d ans !\n", prenom, age);

    return 0;
}
```

> **Important :** `scanf` avec `%s` ne lit pas les espaces. Pour lire une ligne entière :
> ```c
> fgets(prenom, sizeof(prenom), stdin);
> ```

---

## Commentaires

```c
// Commentaire sur une ligne (C99+)

/* Commentaire
   sur plusieurs
   lignes */
```

---

## Exercice — Calculatrice simple

```c
#include <stdio.h>

int main() {
    double a, b;

    printf("Entrer deux nombres : ");
    scanf("%lf %lf", &a, &b);

    printf("Somme      : %.2f\n", a + b);
    printf("Différence : %.2f\n", a - b);
    printf("Produit    : %.2f\n", a * b);

    if (b != 0)
        printf("Quotient   : %.2f\n", a / b);
    else
        printf("Division par zéro impossible !\n");

    return 0;
}
```

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Compiler en ligne | [https://www.onlinegdb.com/online_c_compiler](https://www.onlinegdb.com/online_c_compiler) | Compiler C sans installation |
| Exercism C | [https://exercism.org/tracks/c](https://exercism.org/tracks/c) | Exercices avec feedback |
| Learn-C.org | [https://www.learn-c.org/](https://www.learn-c.org/) | Tutoriel interactif |
| OpenClassrooms C | [https://openclassrooms.com/fr/courses/19980-apprenez-a-programmer-en-c](https://openclassrooms.com/fr/courses/19980-apprenez-a-programmer-en-c) | Cours en français |

---

[← Retour au README](../README.md) | [Chapitre suivant : Variables →](02-variables.md)
