# Chapitre 04 — C : Fonctions

## Syntaxe des fonctions en C

```c
type_retour nom_fonction(type param1, type param2) {
    /* Corps */
    return valeur;
}
```

---

## Exemple de base

```c
#include <stdio.h>

/* Prototype (déclaration avant main) */
int additionner(int a, int b);
void afficherBonjour(char* nom);

int main() {
    int result = additionner(5, 3);
    printf("5 + 3 = %d\n", result);

    afficherBonjour("Alice");

    return 0;
}

/* Définition */
int additionner(int a, int b) {
    return a + b;
}

void afficherBonjour(char* nom) {
    printf("Bonjour %s !\n", nom);
}
```

---

## Passage par valeur

En C, les arguments sont passés **par valeur** (copie). La fonction ne peut pas modifier la variable originale.

```c
void doubler(int x) {
    x = x * 2;  /* Modifie la copie */
}

int a = 5;
doubler(a);
printf("%d\n", a);  /* 5 — inchangé ! */
```

---

## Passage par pointeur (simulation du passage par référence)

Pour modifier une variable depuis une fonction, on passe son **adresse**.

```c
void doubler(int* x) {
    *x = *x * 2;  /* Modifie la valeur pointée */
}

int a = 5;
doubler(&a);        /* Passer l'adresse de a */
printf("%d\n", a);  /* 10 — modifié ! */
```

### Exemple : swap (échanger deux valeurs)

```c
void swap(int* a, int* b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

int x = 5, y = 10;
printf("Avant : x=%d, y=%d\n", x, y);
swap(&x, &y);
printf("Après : x=%d, y=%d\n", x, y); /* x=10, y=5 */
```

---

## Fonctions avec tableaux

En C, passer un tableau à une fonction revient à passer un **pointeur vers son premier élément**.

```c
#include <stdio.h>

/* On doit passer la taille séparément */
void afficher(int tab[], int taille) {
    for (int i = 0; i < taille; i++) {
        printf("%d ", tab[i]);
    }
    printf("\n");
}

int somme(int* tab, int taille) {
    int s = 0;
    for (int i = 0; i < taille; i++) {
        s += tab[i];
    }
    return s;
}

int main() {
    int notes[] = {15, 12, 18, 9, 14};
    int n = sizeof(notes) / sizeof(notes[0]);  /* 5 */

    afficher(notes, n);
    printf("Somme : %d\n", somme(notes, n));

    return 0;
}
```

---

## Récursivité

```c
/* Factorielle */
int factorielle(int n) {
    if (n <= 1) return 1;         /* Cas de base */
    return n * factorielle(n - 1); /* Appel récursif */
}

printf("%d\n", factorielle(5)); /* 120 */

/* Fibonacci */
int fibonacci(int n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
}

printf("%d\n", fibonacci(10)); /* 55 */

/* Somme récursive */
int sommeRec(int n) {
    if (n <= 0) return 0;
    return n + sommeRec(n - 1);
}

printf("%d\n", sommeRec(10)); /* 55 */
```

---

## Fonctions qui retournent des pointeurs

```c
#include <stdlib.h>  /* malloc */

/* Allouer et retourner un tableau */
int* creerTableau(int taille) {
    int* tab = malloc(taille * sizeof(int));
    for (int i = 0; i < taille; i++) {
        tab[i] = i * 2;
    }
    return tab;
}

int main() {
    int* t = creerTableau(5);
    for (int i = 0; i < 5; i++) {
        printf("%d ", t[i]);  /* 0 2 4 6 8 */
    }
    free(t);  /* Libérer la mémoire ! */
    return 0;
}
```

---

## Portée des variables

```c
int globale = 100;  /* Variable globale — accessible partout */

void fonction() {
    int locale = 5;              /* Variable locale — uniquement dans cette fonction */
    static int compteur = 0;     /* Variable statique — conserve sa valeur entre appels */
    compteur++;
    printf("Appelé %d fois\n", compteur);
    printf("Globale : %d\n", globale);
}

int main() {
    fonction();  /* "Appelé 1 fois" */
    fonction();  /* "Appelé 2 fois" */
    fonction();  /* "Appelé 3 fois" */
    /* printf("%d\n", locale); // ERREUR : locale n'existe pas ici */
    return 0;
}
```

---

## Exemple complet — Calculatrice modulaire

```c
#include <stdio.h>

double additionner(double a, double b) { return a + b; }
double soustraire(double a, double b) { return a - b; }
double multiplier(double a, double b) { return a * b; }

double diviser(double a, double b) {
    if (b == 0) {
        printf("Erreur : division par zéro !\n");
        return 0;
    }
    return a / b;
}

void afficherMenu() {
    printf("\n1. Addition\n");
    printf("2. Soustraction\n");
    printf("3. Multiplication\n");
    printf("4. Division\n");
    printf("5. Quitter\n");
    printf("Choix : ");
}

int main() {
    int choix;
    double a, b;

    do {
        afficherMenu();
        scanf("%d", &choix);

        if (choix >= 1 && choix <= 4) {
            printf("Nombre 1 : ");
            scanf("%lf", &a);
            printf("Nombre 2 : ");
            scanf("%lf", &b);
        }

        switch (choix) {
            case 1: printf("= %.2f\n", additionner(a, b)); break;
            case 2: printf("= %.2f\n", soustraire(a, b)); break;
            case 3: printf("= %.2f\n", multiplier(a, b)); break;
            case 4: printf("= %.2f\n", diviser(a, b));    break;
            case 5: printf("Au revoir !\n"); break;
            default: printf("Choix invalide.\n");
        }
    } while (choix != 5);

    return 0;
}
```

---

## Exercices

1. Écrire `int estPremier(int n)` qui retourne 1 si n est premier, 0 sinon
2. Écrire `void inverserTableau(int tab[], int taille)` qui inverse le tableau en place
3. Écrire `int puissance(int base, int exp)` récursivement
4. Écrire `int compterVoyelles(char* s)` qui compte les voyelles d'une chaîne

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Learn-C.org | [https://www.learn-c.org/en/Functions](https://www.learn-c.org/en/Functions) | Fonctions en C interactif |
| Exercism C | [https://exercism.org/tracks/c](https://exercism.org/tracks/c) | Exercices avec corrections |

---

[← Structures de contrôle](03-controle.md) | [Retour au README](../README.md) | [Tableaux et chaînes →](05-tableaux.md)
