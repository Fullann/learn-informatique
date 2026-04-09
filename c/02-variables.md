# Chapitre 02 — C : Variables et types

## Les types fondamentaux en C

En C, les types sont plus stricts qu'en C++. Il n'y a pas de `string` natif ni de `bool` avant C99.

```c
#include <stdio.h>
#include <stdbool.h>  /* Pour bool (C99) */

int main() {
    /* Entiers */
    int age = 25;               /* -2 147 483 648 à 2 147 483 647 */
    short petit = 100;          /* -32 768 à 32 767 */
    long grand = 1000000L;      /* Au moins 32 bits */
    long long tresGrand = 9000000000LL;
    unsigned int positif = 42;  /* Uniquement positif */

    /* Décimaux */
    float prix = 3.14f;         /* ~7 chiffres significatifs */
    double precision = 3.14159265; /* ~15 chiffres significatifs */

    /* Caractère */
    char lettre = 'A';          /* Un seul caractère */
    char code = 65;             /* ASCII de 'A' */

    /* Booléen (C99 avec stdbool.h) */
    bool estVrai = true;
    bool estFaux = false;

    /* Afficher les tailles */
    printf("int    : %zu octets\n", sizeof(int));
    printf("float  : %zu octets\n", sizeof(float));
    printf("double : %zu octets\n", sizeof(double));
    printf("char   : %zu octet\n",  sizeof(char));

    return 0;
}
```

---

## Déclaration et initialisation

En C classique (avant C99), les variables **doivent être déclarées en début de bloc**.

```c
int main() {
    /* C89/C90 : toutes les déclarations en premier */
    int x;
    int y = 10;
    float f = 3.14f;

    /* Puis le code */
    x = 5;
    printf("%d\n", x + y);

    return 0;
}
```

Avec C99 et après, tu peux déclarer où tu veux :

```c
for (int i = 0; i < 10; i++) { /* C99+ */
    printf("%d\n", i);
}
```

---

## Constantes

```c
/* Avec #define (préprocesseur) */
#define PI 3.14159265
#define MAX 100
#define TAILLE_NOM 50

/* Avec const (C89+) */
const double GRAVITE = 9.81;
const int JOURS_SEMAINE = 7;

printf("PI = %f\n", PI);
printf("Gravité = %.2f m/s²\n", GRAVITE);
```

---

## Opérateurs

```c
int a = 10, b = 3;

/* Arithmétique */
printf("%d\n", a + b);   /* 13 */
printf("%d\n", a - b);   /* 7  */
printf("%d\n", a * b);   /* 30 */
printf("%d\n", a / b);   /* 3  (division entière) */
printf("%d\n", a % b);   /* 1  (modulo) */

/* Division réelle */
printf("%f\n", (double)a / b); /* 3.333333 */

/* Assignation raccourcie */
a += 5;   /* a = 15 */
a -= 3;   /* a = 12 */
a *= 2;   /* a = 24 */
a /= 4;   /* a = 6  */
a %= 4;   /* a = 2  */
a++;      /* a = 3  */
a--;      /* a = 2  */

/* Comparaison */
printf("%d\n", a == b);  /* 0 (faux) */
printf("%d\n", a != b);  /* 1 (vrai) */
printf("%d\n", a < b);   /* 1 (vrai) */

/* Logique */
printf("%d\n", 1 && 0);  /* 0 */
printf("%d\n", 1 || 0);  /* 1 */
printf("%d\n", !1);       /* 0 */
```

---

## Conversion de types (casting)

```c
int a = 7, b = 2;

/* Division entière */
printf("%d\n", a / b);            /* 3 */

/* Cast pour division réelle */
printf("%f\n", (double)a / b);    /* 3.500000 */
printf("%.2f\n", (float)a / b);   /* 3.50 */

/* Perte de données */
double pi = 3.14159;
int piEntier = (int)pi;           /* 3 */
printf("%d\n", piEntier);

/* char ↔ int (codes ASCII) */
char c = 'A';
printf("%d\n", (int)c);   /* 65 */
printf("%c\n", (char)66); /* B  */
```

---

## Les chaînes de caractères en C

En C, il n'y a pas de type `string`. Les chaînes sont des **tableaux de `char`** terminés par `'\0'` (caractère nul).

```c
#include <stdio.h>
#include <string.h>  /* Pour les fonctions de chaînes */

int main() {
    /* Déclaration */
    char prenom[20] = "Alice";     /* Tableau de 20 chars */
    char nom[] = "Dupont";         /* Taille déduite : 7 */
    char vide[50];                 /* Non initialisé */

    /* Afficher */
    printf("%s\n", prenom);

    /* Longueur */
    printf("%zu\n", strlen(prenom));  /* 5 */

    /* Copier */
    strcpy(vide, prenom);             /* Copie "Alice" dans vide */

    /* Concaténer */
    char complet[50] = "Alice";
    strcat(complet, " ");
    strcat(complet, "Dupont");
    printf("%s\n", complet);          /* "Alice Dupont" */

    /* Comparer */
    if (strcmp(prenom, "Alice") == 0) {
        printf("C'est Alice !\n");
    }

    /* Chercher un sous-chaîne */
    char* pos = strstr(complet, "Dupont");
    if (pos != NULL) {
        printf("Trouvé : %s\n", pos); /* "Dupont" */
    }

    /* Lire une ligne entière */
    char ligne[100];
    printf("Entrer une phrase : ");
    fgets(ligne, sizeof(ligne), stdin);
    printf("Tu as écrit : %s", ligne);

    return 0;
}
```

### Fonctions de string.h

| Fonction | Description |
|----------|-------------|
| `strlen(s)` | Longueur de la chaîne |
| `strcpy(dst, src)` | Copier une chaîne |
| `strncpy(dst, src, n)` | Copier n caractères |
| `strcat(dst, src)` | Concaténer |
| `strncat(dst, src, n)` | Concaténer n caractères |
| `strcmp(a, b)` | Comparer (0 = égal) |
| `strncmp(a, b, n)` | Comparer n chars |
| `strstr(haystack, needle)` | Chercher une sous-chaîne |
| `strchr(s, c)` | Chercher un caractère |
| `sprintf(buf, fmt, ...)` | Écrire dans un buffer |

---

## Exercice

Écris un programme qui :
1. Demande le prénom et le nom de l'utilisateur
2. Concatène les deux en un nom complet
3. Affiche le nom complet en MAJUSCULES (voir `toupper()` dans `<ctype.h>`)
4. Affiche le nombre de caractères

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Learn-C.org | [https://www.learn-c.org/en/Variables_and_Types](https://www.learn-c.org/en/Variables_and_Types) | Types en C interactif |
| Exercism C | [https://exercism.org/tracks/c](https://exercism.org/tracks/c) | Exercices pratiques |

---

[← Introduction](01-introduction.md) | [Retour au README](../README.md) | [Structures de contrôle →](03-controle.md)
