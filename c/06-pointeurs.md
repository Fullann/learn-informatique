# Chapitre 06 — C : Pointeurs

## Qu'est-ce qu'un pointeur ?

Un **pointeur** est une variable qui stocke l'**adresse mémoire** d'une autre variable.

```
Variable a :   adresse 0x7ffd1234
               valeur  [42]

Pointeur p :   adresse 0x7ffd1240
               valeur  [0x7ffd1234]  ← stocke l'adresse de a
```

C'est l'une des notions les plus puissantes (et dangereuses) du C.

---

## Déclarer et utiliser un pointeur

```c
#include <stdio.h>

int main() {
    int a = 42;

    /* Déclarer un pointeur vers un int */
    int* p;

    /* & = opérateur "adresse de" */
    p = &a;  /* p contient l'adresse de a */

    /* Afficher l'adresse et la valeur */
    printf("Valeur de a    : %d\n",  a);
    printf("Adresse de a   : %p\n",  (void*)&a);
    printf("Pointeur p     : %p\n",  (void*)p);

    /* * = opérateur "déréférencement" : accéder à la valeur pointée */
    printf("Valeur via *p  : %d\n",  *p);  /* 42 */

    /* Modifier via le pointeur */
    *p = 100;
    printf("a après *p=100 : %d\n", a);  /* 100 */

    return 0;
}
```

---

## Pointeur nul

```c
int* p = NULL;  /* Pointeur vide, ne pointe nulle part */

/* Toujours vérifier avant de déréférencer */
if (p != NULL) {
    printf("%d\n", *p);
}
/* *p ici = comportement indéfini (crash probable) */
```

---

## Pointeurs et fonctions

```c
#include <stdio.h>

/* Passage par pointeur — modifie la variable originale */
void doubler(int* x) {
    *x = *x * 2;
}

void swap(int* a, int* b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

/* Retourner plusieurs valeurs via pointeurs */
void minMax(int tab[], int n, int* min, int* max) {
    *min = *max = tab[0];
    for (int i = 1; i < n; i++) {
        if (tab[i] < *min) *min = tab[i];
        if (tab[i] > *max) *max = tab[i];
    }
}

int main() {
    int a = 5;
    doubler(&a);
    printf("a = %d\n", a);  /* 10 */

    int x = 3, y = 7;
    swap(&x, &y);
    printf("x=%d, y=%d\n", x, y);  /* x=7, y=3 */

    int notes[] = {5, 12, 8, 18, 3};
    int min, max;
    minMax(notes, 5, &min, &max);
    printf("Min=%d, Max=%d\n", min, max);  /* Min=3, Max=18 */

    return 0;
}
```

---

## Pointeurs et tableaux

En C, le nom d'un tableau est un **pointeur constant** vers son premier élément.

```c
int tab[] = {10, 20, 30, 40, 50};

int* p = tab;  /* Même chose que int* p = &tab[0] */

/* Ces deux notations sont équivalentes */
printf("%d\n", tab[2]);     /* 30 */
printf("%d\n", *(tab + 2)); /* 30 */

/* Arithmétique de pointeurs */
printf("%d\n", *p);         /* 10 */
p++;                         /* Avancer d'un int */
printf("%d\n", *p);         /* 20 */
p += 2;
printf("%d\n", *p);         /* 40 */
```

---

## Pointeurs et chaînes

```c
char str[] = "Bonjour";
char* p = str;

/* Parcourir */
while (*p != '\0') {
    printf("%c", *p);
    p++;
}
printf("\n");

/* Les littéraux de chaînes sont des pointeurs constants */
char* message = "Salut";  /* Pointeur vers une chaîne en mémoire lecture seule */
/* message[0] = 'B'; // Interdit ! Comportement indéfini */

/* Pour modifier, utiliser un tableau */
char modif[] = "Salut";
modif[0] = 'B';  /* OK */
```

---

## Pointeurs de pointeurs

```c
int a = 42;
int* p = &a;    /* Pointeur vers int */
int** pp = &p;  /* Pointeur vers pointeur vers int */

printf("%d\n", a);    /* 42 */
printf("%d\n", *p);   /* 42 */
printf("%d\n", **pp); /* 42 */

**pp = 100;
printf("%d\n", a);    /* 100 */
```

---

## Allocation dynamique de mémoire

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int n;
    printf("Combien d'entiers ? ");
    scanf("%d", &n);

    /* malloc — allouer n * sizeof(int) octets */
    int* tab = malloc(n * sizeof(int));

    if (tab == NULL) {
        printf("Erreur d'allocation !\n");
        return 1;
    }

    for (int i = 0; i < n; i++) {
        printf("tab[%d] = ", i);
        scanf("%d", &tab[i]);
    }

    printf("Tu as saisi : ");
    for (int i = 0; i < n; i++) {
        printf("%d ", tab[i]);
    }
    printf("\n");

    /* OBLIGATOIRE : libérer la mémoire */
    free(tab);
    tab = NULL;  /* Bonne pratique */

    return 0;
}
```

### calloc et realloc

```c
/* calloc — comme malloc mais initialise à zéro */
int* tab = calloc(10, sizeof(int));

/* realloc — redimensionner un tableau alloué */
tab = realloc(tab, 20 * sizeof(int));
if (tab == NULL) { /* Gérer l'erreur */ }

free(tab);
```

---

## Pointeurs de fonctions

```c
#include <stdio.h>

int additionner(int a, int b) { return a + b; }
int soustraire(int a, int b) { return a - b; }
int multiplier(int a, int b) { return a * b; }

int main() {
    /* Déclarer un pointeur de fonction */
    int (*operation)(int, int);

    operation = additionner;
    printf("3 + 4 = %d\n", operation(3, 4)); /* 7 */

    operation = soustraire;
    printf("3 - 4 = %d\n", operation(3, 4)); /* -1 */

    /* Tableau de pointeurs de fonctions */
    int (*ops[3])(int, int) = {additionner, soustraire, multiplier};
    char* noms[] = {"Addition", "Soustraction", "Multiplication"};

    for (int i = 0; i < 3; i++) {
        printf("%s(5, 3) = %d\n", noms[i], ops[i](5, 3));
    }

    return 0;
}
```

---

## Les erreurs classiques avec les pointeurs

```c
/* 1. Déréférencer un pointeur nul */
int* p = NULL;
// *p = 42; // CRASH

/* 2. Utiliser de la mémoire non initialisée */
int* q;
// printf("%d", *q); // Comportement indéfini

/* 3. Utiliser après free (use-after-free) */
int* t = malloc(sizeof(int));
free(t);
// *t = 5; // Comportement indéfini

/* 4. Double free */
// free(t); // CRASH

/* 5. Buffer overflow */
char buf[5];
// strcpy(buf, "une chaîne trop longue"); // Dépassement de tampon

/* Bonne pratique : toujours vérifier malloc, toujours free */
```

---

## Exercices

1. Écrire une fonction `void trier(int* tab, int n)` qui trie un tableau via pointeurs
2. Écrire une fonction `char* dupliquer(const char* s)` qui retourne une copie allouée dynamiquement
3. Écrire un programme qui lit N entiers dynamiquement, calcule la moyenne puis libère la mémoire
4. Simuler une pile (stack) avec `malloc` et `realloc`

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Learn-C.org | [https://www.learn-c.org/en/Pointers](https://www.learn-c.org/en/Pointers) | Pointeurs interactifs |
| Visualiser la mémoire | [https://pythontutor.com/c.html](https://pythontutor.com/c.html) | Visualiser l'exécution pas à pas |

---

[← Tableaux](05-tableaux.md) | [Retour au README](../README.md) | [Structures →](07-structures.md)
