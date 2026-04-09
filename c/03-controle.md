# Chapitre 03 — C : Structures de contrôle

## if / else if / else

```c
#include <stdio.h>

int main() {
    int note = 75;

    if (note >= 90) {
        printf("Très bien !\n");
    } else if (note >= 70) {
        printf("Bien\n");
    } else if (note >= 50) {
        printf("Passable\n");
    } else {
        printf("Insuffisant\n");
    }

    /* Opérateur ternaire */
    int age = 20;
    char* statut = (age >= 18) ? "majeur" : "mineur";
    printf("Statut : %s\n", statut);

    return 0;
}
```

---

## switch

```c
int choix = 2;

switch (choix) {
    case 1:
        printf("Option 1\n");
        break;
    case 2:
        printf("Option 2\n");
        break;
    case 3:
    case 4:
        printf("Option 3 ou 4\n");
        break;
    default:
        printf("Choix invalide\n");
}
```

> Sans `break`, l'exécution continue dans le `case` suivant (fall-through).

---

## Boucle for

```c
/* Compter de 0 à 4 */
for (int i = 0; i < 5; i++) {
    printf("%d ", i);  /* 0 1 2 3 4 */
}
printf("\n");

/* Compter à rebours */
for (int i = 10; i > 0; i--) {
    printf("%d ", i);  /* 10 9 8 7 ... 1 */
}
printf("\n");

/* Pas de 2 */
for (int i = 0; i <= 10; i += 2) {
    printf("%d ", i);  /* 0 2 4 6 8 10 */
}
```

---

## Boucle while

```c
int i = 0;
while (i < 5) {
    printf("%d\n", i);
    i++;
}

/* Lire jusqu'à saisie valide */
int n;
printf("Entrer un nombre positif : ");
scanf("%d", &n);
while (n <= 0) {
    printf("Erreur ! Entrer un nombre positif : ");
    scanf("%d", &n);
}
printf("OK : %d\n", n);
```

---

## Boucle do-while

```c
/* S'exécute au moins une fois */
int x;
do {
    printf("Entrer un nombre entre 1 et 10 : ");
    scanf("%d", &x);
} while (x < 1 || x > 10);

printf("Tu as choisi : %d\n", x);
```

---

## break et continue

```c
/* break — quitter la boucle */
for (int i = 0; i < 10; i++) {
    if (i == 5) break;
    printf("%d ", i);  /* 0 1 2 3 4 */
}

/* continue — passer à l'itération suivante */
for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) continue;  /* Ignorer les pairs */
    printf("%d ", i);           /* 1 3 5 7 9 */
}
```

---

## Exemples pratiques

### Deviner un nombre

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main() {
    srand(time(NULL));
    int secret = rand() % 100 + 1;
    int tentative, essais = 0;

    printf("Devine le nombre entre 1 et 100 !\n");

    do {
        printf("Ta tentative : ");
        scanf("%d", &tentative);
        essais++;

        if (tentative < secret)
            printf("C'est plus !\n");
        else if (tentative > secret)
            printf("C'est moins !\n");
        else
            printf("Bravo ! Trouvé en %d essais !\n", essais);

    } while (tentative != secret);

    return 0;
}
```

### Table de multiplication

```c
#include <stdio.h>

int main() {
    int n;
    printf("Table de multiplication de : ");
    scanf("%d", &n);

    for (int i = 1; i <= 10; i++) {
        printf("%d x %d = %d\n", n, i, n * i);
    }

    return 0;
}
```

### FizzBuzz

```c
for (int i = 1; i <= 100; i++) {
    if (i % 15 == 0)
        printf("FizzBuzz\n");
    else if (i % 3 == 0)
        printf("Fizz\n");
    else if (i % 5 == 0)
        printf("Buzz\n");
    else
        printf("%d\n", i);
}
```

### Suite de Fibonacci

```c
#include <stdio.h>

int main() {
    int n;
    printf("Combien de termes ? ");
    scanf("%d", &n);

    int a = 0, b = 1;
    for (int i = 0; i < n; i++) {
        printf("%d ", a);
        int temp = a + b;
        a = b;
        b = temp;
    }
    printf("\n");

    return 0;
}
```

---

## Exercices

1. Écrire un programme qui affiche tous les nombres premiers entre 1 et 100
2. Écrire un programme qui calcule la somme des chiffres d'un entier saisi
3. Écrire un programme qui détermine si un nombre est un palindrome (ex: 121)
4. Écrire un programme de menu avec boucle : ajouter, voir, quitter

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Learn-C.org | [https://www.learn-c.org/en/Conditions](https://www.learn-c.org/en/Conditions) | Conditions en C |
| HackerRank C | [https://www.hackerrank.com/domains/c](https://www.hackerrank.com/domains/c) | Challenges C |

---

[← Variables](02-variables.md) | [Retour au README](../README.md) | [Fonctions →](04-fonctions.md)
