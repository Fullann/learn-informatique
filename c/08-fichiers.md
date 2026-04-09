# Chapitre 08 — C : Fichiers

## Pourquoi les fichiers ?

Jusqu'ici, les données de nos programmes disparaissent à la fin de l'exécution. Les **fichiers** permettent de **persister des données** sur le disque dur.

Usages : sauvegarder des scores, lire des configurations, traiter des données CSV, journaux (logs), etc.

---

## Ouvrir et fermer un fichier

```c
#include <stdio.h>

int main() {
    FILE* fichier;

    /* Ouvrir en écriture */
    fichier = fopen("donnees.txt", "w");

    if (fichier == NULL) {
        printf("Impossible d'ouvrir le fichier !\n");
        return 1;
    }

    fprintf(fichier, "Bonjour depuis C !\n");
    fprintf(fichier, "Ligne 2\n");

    /* Toujours fermer le fichier */
    fclose(fichier);

    return 0;
}
```

### Modes d'ouverture

| Mode | Description |
|------|-------------|
| `"r"` | Lire (le fichier doit exister) |
| `"w"` | Écrire (crée ou écrase) |
| `"a"` | Ajouter à la fin |
| `"r+"` | Lire et écrire |
| `"w+"` | Lire et écrire (crée ou écrase) |
| `"rb"` | Lire en binaire |
| `"wb"` | Écrire en binaire |

---

## Écrire dans un fichier

```c
#include <stdio.h>

int main() {
    FILE* f = fopen("resultats.txt", "w");
    if (f == NULL) return 1;

    /* fprintf — comme printf mais vers un fichier */
    fprintf(f, "Nom : Alice\n");
    fprintf(f, "Âge : %d\n", 25);
    fprintf(f, "Note : %.2f\n", 16.5f);

    /* fputs — écrire une chaîne */
    fputs("Une autre ligne\n", f);

    /* fputc — écrire un caractère */
    fputc('X', f);
    fputc('\n', f);

    fclose(f);
    printf("Fichier écrit avec succès.\n");
    return 0;
}
```

---

## Lire depuis un fichier

```c
#include <stdio.h>

int main() {
    FILE* f = fopen("resultats.txt", "r");
    if (f == NULL) {
        printf("Fichier introuvable !\n");
        return 1;
    }

    char ligne[256];

    /* fgets — lire ligne par ligne (recommandé) */
    while (fgets(ligne, sizeof(ligne), f) != NULL) {
        printf("%s", ligne);  /* ligne contient déjà le \n */
    }

    fclose(f);
    return 0;
}
```

### Autres méthodes de lecture

```c
/* fscanf — comme scanf mais depuis un fichier */
FILE* f = fopen("donnees.txt", "r");
char nom[50];
int age;
float note;

while (fscanf(f, "%s %d %f", nom, &age, &note) == 3) {
    printf("%s : %d ans, %.1f\n", nom, age, note);
}
fclose(f);

/* fgetc — lire caractère par caractère */
FILE* f2 = fopen("texte.txt", "r");
int c;
while ((c = fgetc(f2)) != EOF) {
    putchar(c);
}
fclose(f2);
```

---

## Vérifier la fin de fichier

```c
FILE* f = fopen("data.txt", "r");
char ligne[256];

while (!feof(f)) {
    if (fgets(ligne, sizeof(ligne), f) != NULL) {
        printf("%s", ligne);
    }
}

fclose(f);
```

---

## Exemple complet — Carnet d'adresses

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define FICHIER "contacts.txt"
#define MAX 100

typedef struct {
    char nom[50];
    char telephone[20];
    char email[50];
} Contact;

/* Sauvegarder tous les contacts dans le fichier */
void sauvegarder(Contact contacts[], int nb) {
    FILE* f = fopen(FICHIER, "w");
    if (f == NULL) {
        printf("Erreur de sauvegarde !\n");
        return;
    }
    fprintf(f, "%d\n", nb);
    for (int i = 0; i < nb; i++) {
        fprintf(f, "%s\n%s\n%s\n",
            contacts[i].nom,
            contacts[i].telephone,
            contacts[i].email);
    }
    fclose(f);
    printf("Contacts sauvegardés.\n");
}

/* Charger les contacts depuis le fichier */
int charger(Contact contacts[]) {
    FILE* f = fopen(FICHIER, "r");
    if (f == NULL) return 0;  /* Pas de fichier = 0 contacts */

    int nb;
    fscanf(f, "%d\n", &nb);
    for (int i = 0; i < nb; i++) {
        fgets(contacts[i].nom,       sizeof(contacts[i].nom),       f);
        fgets(contacts[i].telephone, sizeof(contacts[i].telephone), f);
        fgets(contacts[i].email,     sizeof(contacts[i].email),     f);
        /* Supprimer le \n final */
        contacts[i].nom[strcspn(contacts[i].nom, "\n")] = '\0';
        contacts[i].telephone[strcspn(contacts[i].telephone, "\n")] = '\0';
        contacts[i].email[strcspn(contacts[i].email, "\n")] = '\0';
    }
    fclose(f);
    return nb;
}

void afficherTous(Contact contacts[], int nb) {
    if (nb == 0) {
        printf("Aucun contact.\n");
        return;
    }
    printf("\n=== %d Contact(s) ===\n", nb);
    for (int i = 0; i < nb; i++) {
        printf("[%d] %s | %s | %s\n",
            i + 1,
            contacts[i].nom,
            contacts[i].telephone,
            contacts[i].email);
    }
}

int main() {
    Contact contacts[MAX];
    int nb = charger(contacts);
    int choix;

    do {
        printf("\n1. Ajouter\n2. Afficher\n3. Quitter\nChoix : ");
        scanf("%d", &choix);
        getchar();  /* Consommer le \n */

        switch (choix) {
            case 1:
                if (nb >= MAX) {
                    printf("Liste pleine !\n");
                    break;
                }
                printf("Nom : ");
                fgets(contacts[nb].nom, sizeof(contacts[nb].nom), stdin);
                contacts[nb].nom[strcspn(contacts[nb].nom, "\n")] = '\0';

                printf("Téléphone : ");
                fgets(contacts[nb].telephone, sizeof(contacts[nb].telephone), stdin);
                contacts[nb].telephone[strcspn(contacts[nb].telephone, "\n")] = '\0';

                printf("Email : ");
                fgets(contacts[nb].email, sizeof(contacts[nb].email), stdin);
                contacts[nb].email[strcspn(contacts[nb].email, "\n")] = '\0';

                nb++;
                sauvegarder(contacts, nb);
                break;

            case 2:
                afficherTous(contacts, nb);
                break;

            case 3:
                printf("Au revoir !\n");
                break;

            default:
                printf("Choix invalide.\n");
        }
    } while (choix != 3);

    return 0;
}
```

---

## Fichiers binaires

Pour stocker des structures directement (plus rapide, mais non lisible en texte).

```c
#include <stdio.h>

typedef struct {
    char nom[50];
    int score;
} Score;

int main() {
    /* Écrire en binaire */
    Score scores[] = {{"Alice", 1500}, {"Bob", 1200}, {"Charlie", 1800}};
    int n = 3;

    FILE* f = fopen("scores.bin", "wb");
    fwrite(&n, sizeof(int), 1, f);           /* Nombre d'entrées */
    fwrite(scores, sizeof(Score), n, f);     /* Les structures */
    fclose(f);

    /* Lire en binaire */
    FILE* f2 = fopen("scores.bin", "rb");
    int nb;
    fread(&nb, sizeof(int), 1, f2);

    Score lus[10];
    fread(lus, sizeof(Score), nb, f2);
    fclose(f2);

    for (int i = 0; i < nb; i++) {
        printf("%s : %d\n", lus[i].nom, lus[i].score);
    }

    return 0;
}
```

---

## Exercices

1. Écrire un programme qui copie un fichier texte vers un autre fichier
2. Écrire un programme qui compte le nombre de lignes, mots et caractères d'un fichier
3. Écrire un programme qui lit un fichier CSV (nom,note) et calcule la moyenne
4. Étendre le carnet d'adresses avec une fonction de recherche par nom

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Learn-C.org | [https://www.learn-c.org/en/File_IO](https://www.learn-c.org/en/File_IO) | Fichiers en C interactif |
| Exercism C | [https://exercism.org/tracks/c](https://exercism.org/tracks/c) | Exercices pratiques |
| TutorialsPoint C | [https://www.tutorialspoint.com/cprogramming/c_file_io.htm](https://www.tutorialspoint.com/cprogramming/c_file_io.htm) | Tutoriel fichiers C |

---

[← Structures](07-structures.md) | [Retour au README](../README.md)
