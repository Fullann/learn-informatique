# Chapitre 05 — C : Tableaux et chaînes

## Tableaux à une dimension

```c
#include <stdio.h>

int main() {
    /* Déclaration */
    int notes[5];                          /* 5 entiers non initialisés */
    int scores[5] = {10, 20, 30, 40, 50}; /* Initialisé */
    int auto_size[] = {1, 2, 3, 4};        /* Taille déduite : 4 */
    int zeros[5] = {0};                    /* Tous à zéro */

    /* Accès (commence à 0) */
    printf("%d\n", scores[0]);  /* 10 */
    printf("%d\n", scores[4]);  /* 50 */

    /* Modifier */
    scores[2] = 99;

    /* Taille */
    int taille = sizeof(scores) / sizeof(scores[0]);
    printf("Taille : %d\n", taille);  /* 5 */

    /* Parcourir */
    for (int i = 0; i < taille; i++) {
        printf("%d ", scores[i]);
    }
    printf("\n");

    return 0;
}
```

---

## Tableaux à deux dimensions

```c
int grille[3][3] = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

/* Accès */
printf("%d\n", grille[1][2]);  /* 6 */

/* Parcourir */
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        printf("%d ", grille[i][j]);
    }
    printf("\n");
}
```

---

## Opérations courantes sur les tableaux

```c
#include <stdio.h>

/* Trouver le maximum */
int maximum(int tab[], int taille) {
    int max = tab[0];
    for (int i = 1; i < taille; i++) {
        if (tab[i] > max) max = tab[i];
    }
    return max;
}

/* Calculer la moyenne */
double moyenne(int tab[], int taille) {
    int somme = 0;
    for (int i = 0; i < taille; i++) somme += tab[i];
    return (double)somme / taille;
}

/* Tri par sélection */
void triSelection(int tab[], int taille) {
    for (int i = 0; i < taille - 1; i++) {
        int minIdx = i;
        for (int j = i + 1; j < taille; j++) {
            if (tab[j] < tab[minIdx]) minIdx = j;
        }
        /* Échanger */
        int temp = tab[i];
        tab[i] = tab[minIdx];
        tab[minIdx] = temp;
    }
}

/* Tri à bulles */
void triBulles(int tab[], int taille) {
    for (int i = 0; i < taille - 1; i++) {
        for (int j = 0; j < taille - i - 1; j++) {
            if (tab[j] > tab[j + 1]) {
                int temp = tab[j];
                tab[j] = tab[j + 1];
                tab[j + 1] = temp;
            }
        }
    }
}

/* Recherche linéaire */
int rechercherLineaire(int tab[], int taille, int valeur) {
    for (int i = 0; i < taille; i++) {
        if (tab[i] == valeur) return i;
    }
    return -1;  /* Non trouvé */
}

int main() {
    int notes[] = {15, 8, 12, 18, 10, 14, 7};
    int n = sizeof(notes) / sizeof(notes[0]);

    printf("Maximum : %d\n", maximum(notes, n));
    printf("Moyenne : %.2f\n", moyenne(notes, n));

    triSelection(notes, n);
    printf("Trié : ");
    for (int i = 0; i < n; i++) printf("%d ", notes[i]);
    printf("\n");

    int pos = rechercherLineaire(notes, n, 12);
    printf("12 trouvé à l'index : %d\n", pos);

    return 0;
}
```

---

## Chaînes de caractères

En C, une chaîne est un **tableau de `char` terminé par `'\0'`**.

```
"Alice" en mémoire : ['A']['l']['i']['c']['e']['\0']
                       0    1    2    3    4    5
```

```c
#include <stdio.h>
#include <string.h>
#include <ctype.h>

int main() {
    char prenom[20] = "Alice";

    /* Longueur (sans le '\0') */
    printf("Longueur : %zu\n", strlen(prenom));  /* 5 */

    /* Accès caractère par caractère */
    for (int i = 0; prenom[i] != '\0'; i++) {
        printf("%c ", prenom[i]);
    }
    printf("\n");

    /* Copier */
    char copie[20];
    strcpy(copie, prenom);

    /* Copier avec limite (plus sûr) */
    char copie2[20];
    strncpy(copie2, prenom, sizeof(copie2) - 1);
    copie2[sizeof(copie2) - 1] = '\0';  /* Assurer la terminaison */

    /* Concaténer */
    char complet[50] = "Alice";
    strcat(complet, " Dupont");
    printf("%s\n", complet);  /* "Alice Dupont" */

    /* Comparer */
    if (strcmp(prenom, "Alice") == 0)
        printf("Égal !\n");

    /* Convertir en majuscules */
    for (int i = 0; prenom[i] != '\0'; i++) {
        prenom[i] = toupper(prenom[i]);
    }
    printf("%s\n", prenom);  /* "ALICE" */

    /* Chercher un caractère */
    char* pos = strchr(complet, ' ');
    if (pos != NULL)
        printf("Espace à la position : %ld\n", pos - complet);

    /* Chercher une sous-chaîne */
    char* sousCh = strstr(complet, "Dupont");
    if (sousCh != NULL)
        printf("Sous-chaîne trouvée : %s\n", sousCh);

    return 0;
}
```

---

## sprintf et sscanf

```c
#include <stdio.h>

int main() {
    /* sprintf — écrire dans une chaîne (comme printf mais vers un buffer) */
    char buffer[100];
    int age = 25;
    sprintf(buffer, "J'ai %d ans", age);
    printf("%s\n", buffer);  /* "J'ai 25 ans" */

    /* Construire une chaîne formatée */
    char nom[50], prenom[50], complet[100];
    sprintf(complet, "%s %s", prenom, nom);

    /* sscanf — lire depuis une chaîne */
    char donnees[] = "Alice 25 1.68";
    char n[20];
    int a;
    float t;
    sscanf(donnees, "%s %d %f", n, &a, &t);
    printf("Nom: %s, Âge: %d, Taille: %.2f\n", n, a, t);

    return 0;
}
```

---

## Tableaux de chaînes

```c
#include <stdio.h>
#include <string.h>

int main() {
    /* Tableau de chaînes */
    char jours[7][10] = {
        "Lundi", "Mardi", "Mercredi",
        "Jeudi", "Vendredi", "Samedi", "Dimanche"
    };

    for (int i = 0; i < 7; i++) {
        printf("%s\n", jours[i]);
    }

    /* Arguments de la ligne de commande */
    return 0;
}

/* main avec arguments */
int main2(int argc, char* argv[]) {
    printf("Nombre d'arguments : %d\n", argc);
    for (int i = 0; i < argc; i++) {
        printf("argv[%d] = %s\n", i, argv[i]);
    }
    return 0;
}
```

---

## Exercices

1. Écrire un programme qui lit 10 notes, calcule et affiche la moyenne, le min et le max
2. Écrire une fonction `void inverser(char* s)` qui inverse une chaîne en place
3. Écrire une fonction `int estPalindrome(char* s)` qui vérifie si une chaîne est un palindrome
4. Écrire un programme de tri de 5 prénoms par ordre alphabétique

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Learn-C.org | [https://www.learn-c.org/en/Arrays](https://www.learn-c.org/en/Arrays) | Tableaux en C |
| Learn-C.org strings | [https://www.learn-c.org/en/Strings](https://www.learn-c.org/en/Strings) | Chaînes en C |

---

[← Fonctions](04-fonctions.md) | [Retour au README](../README.md) | [Pointeurs →](06-pointeurs.md)
