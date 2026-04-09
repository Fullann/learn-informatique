# Chapitre 07 — C : Structures

## Qu'est-ce qu'une structure ?

Une **structure** (`struct`) permet de regrouper plusieurs variables de types différents sous un seul nom. C'est l'ancêtre des classes en C++.

---

## Déclarer et utiliser une structure

```c
#include <stdio.h>
#include <string.h>

/* Déclaration de la structure */
struct Personne {
    char prenom[50];
    char nom[50];
    int age;
    float taille;
};

int main() {
    /* Créer une variable de type struct Personne */
    struct Personne alice;

    /* Assigner des valeurs */
    strcpy(alice.prenom, "Alice");
    strcpy(alice.nom, "Dupont");
    alice.age = 25;
    alice.taille = 1.68f;

    /* Afficher */
    printf("%s %s, %d ans, %.2f m\n", alice.prenom, alice.nom, alice.age, alice.taille);

    /* Initialisation directe */
    struct Personne bob = {"Bob", "Martin", 30, 1.80f};
    printf("%s %s\n", bob.prenom, bob.nom);

    return 0;
}
```

---

## typedef — Simplifier la syntaxe

```c
#include <stdio.h>
#include <string.h>

/* typedef permet d'éviter d'écrire "struct" à chaque fois */
typedef struct {
    char prenom[50];
    int age;
    float taille;
} Personne;

/* Ou avec un nom pour les structures récursives */
typedef struct Noeud {
    int valeur;
    struct Noeud* suivant;  /* Pointeur vers le même type */
} Noeud;

int main() {
    Personne alice = {"Alice", 25, 1.68f};  /* Plus besoin de "struct" */
    printf("%s a %d ans\n", alice.prenom, alice.age);
    return 0;
}
```

---

## Structures et fonctions

```c
typedef struct {
    char nom[50];
    float x;
    float y;
} Point;

/* Passage par valeur (copie) */
float distance(Point a, Point b) {
    float dx = b.x - a.x;
    float dy = b.y - a.y;
    return sqrt(dx*dx + dy*dy);
}

/* Passage par pointeur (plus efficace pour grandes structures) */
void deplacer(Point* p, float dx, float dy) {
    p->x += dx;  /* -> est l'opérateur d'accès via pointeur */
    p->y += dy;
    /* Équivalent : (*p).x += dx; */
}

void afficher(const Point* p) {
    printf("Point(%s) : (%.2f, %.2f)\n", p->nom, p->x, p->y);
}

int main() {
    Point a = {"A", 0.0f, 0.0f};
    Point b = {"B", 3.0f, 4.0f};

    printf("Distance : %.2f\n", distance(a, b));  /* 5.00 */

    deplacer(&a, 1.0f, 1.0f);
    afficher(&a);  /* Point(A) : (1.00, 1.00) */

    return 0;
}
```

---

## Tableaux de structures

```c
#include <stdio.h>
#include <string.h>

typedef struct {
    char nom[50];
    float moyenne;
} Etudiant;

int main() {
    Etudiant classe[3] = {
        {"Alice", 16.5f},
        {"Bob",   11.0f},
        {"Charlie", 14.2f}
    };

    int n = sizeof(classe) / sizeof(classe[0]);

    /* Afficher */
    for (int i = 0; i < n; i++) {
        printf("%s : %.1f/20\n", classe[i].nom, classe[i].moyenne);
    }

    /* Trouver le meilleur */
    int meilleur = 0;
    for (int i = 1; i < n; i++) {
        if (classe[i].moyenne > classe[meilleur].moyenne) {
            meilleur = i;
        }
    }
    printf("Meilleur : %s (%.1f)\n", classe[meilleur].nom, classe[meilleur].moyenne);

    return 0;
}
```

---

## Structures imbriquées

```c
typedef struct {
    int jour;
    int mois;
    int annee;
} Date;

typedef struct {
    char nom[50];
    Date naissance;
    float salaire;
} Employe;

Employe emp = {
    "Alice",
    {15, 6, 1998},  /* Date imbriquée */
    2500.0f
};

printf("%s né le %02d/%02d/%d\n",
    emp.nom,
    emp.naissance.jour,
    emp.naissance.mois,
    emp.naissance.annee);
```

---

## Énumérations (enum)

Les `enum` permettent de définir un ensemble de constantes nommées.

```c
#include <stdio.h>

typedef enum {
    LUNDI = 1,
    MARDI,
    MERCREDI,
    JEUDI,
    VENDREDI,
    SAMEDI,
    DIMANCHE
} Jour;

typedef enum {
    ROUGE,
    VERT,
    BLEU
} Couleur;

void afficherJour(Jour j) {
    char* noms[] = {"", "Lundi", "Mardi", "Mercredi",
                    "Jeudi", "Vendredi", "Samedi", "Dimanche"};
    printf("%s\n", noms[j]);
}

int main() {
    Jour aujourd_hui = MERCREDI;
    afficherJour(aujourd_hui);  /* Mercredi */

    if (aujourd_hui == SAMEDI || aujourd_hui == DIMANCHE) {
        printf("Week-end !\n");
    } else {
        printf("Jour de travail\n");
    }

    return 0;
}
```

---

## Unions

Une `union` permet à plusieurs membres de **partager le même espace mémoire**.

```c
typedef union {
    int entier;
    float decimal;
    char caractere;
} Valeur;

Valeur v;
v.entier = 42;
printf("%d\n", v.entier);  /* 42 */

v.decimal = 3.14f;
printf("%f\n", v.decimal); /* 3.14 — l'entier est maintenant corrompu */
printf("%d\n", v.entier);  /* Résultat indéfini ! */
```

---

## Exemple complet — Gestion d'étudiants

```c
#include <stdio.h>
#include <string.h>

#define MAX_ETUDIANTS 50
#define MAX_NOTES 10

typedef struct {
    char nom[50];
    int numEtudiant;
    float notes[MAX_NOTES];
    int nbNotes;
} Etudiant;

float calculerMoyenne(Etudiant* e) {
    if (e->nbNotes == 0) return 0;
    float somme = 0;
    for (int i = 0; i < e->nbNotes; i++) {
        somme += e->notes[i];
    }
    return somme / e->nbNotes;
}

void afficherEtudiant(Etudiant* e) {
    printf("N°%d - %s | Moyenne : %.2f/20 | %s\n",
        e->numEtudiant,
        e->nom,
        calculerMoyenne(e),
        calculerMoyenne(e) >= 10 ? "REÇU" : "RECALÉ");
}

void ajouterNote(Etudiant* e, float note) {
    if (e->nbNotes < MAX_NOTES && note >= 0 && note <= 20) {
        e->notes[e->nbNotes++] = note;
    }
}

int main() {
    Etudiant classe[MAX_ETUDIANTS];
    int nbEtudiants = 0;

    /* Ajouter des étudiants */
    Etudiant alice = {"Alice Dupont", 1001, {0}, 0};
    ajouterNote(&alice, 15.5f);
    ajouterNote(&alice, 18.0f);
    ajouterNote(&alice, 12.5f);
    classe[nbEtudiants++] = alice;

    Etudiant bob = {"Bob Martin", 1002, {0}, 0};
    ajouterNote(&bob, 7.0f);
    ajouterNote(&bob, 9.5f);
    ajouterNote(&bob, 11.0f);
    classe[nbEtudiants++] = bob;

    /* Afficher */
    printf("=== Résultats ===\n");
    for (int i = 0; i < nbEtudiants; i++) {
        afficherEtudiant(&classe[i]);
    }

    return 0;
}
```

---

## Exercices

1. Créer une structure `Rectangle` avec largeur et hauteur, et des fonctions `aire()` et `perimetre()`
2. Créer une structure `Contact` (nom, téléphone, email) et un programme pour gérer une liste de contacts
3. Créer une structure `Fraction` (numérateur, dénominateur) avec des fonctions `additionner`, `multiplier`, `simplifier`
4. Simuler une file d'attente avec un tableau de structures `Client` (numéro, nom)

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Learn-C.org | [https://www.learn-c.org/en/Structures](https://www.learn-c.org/en/Structures) | Structures en C |
| Exercism C | [https://exercism.org/tracks/c](https://exercism.org/tracks/c) | Exercices pratiques |

---

[← Pointeurs](06-pointeurs.md) | [Retour au README](../README.md) | [Fichiers →](08-fichiers.md)
