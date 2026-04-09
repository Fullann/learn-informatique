# Chapitre 03 — C++ : Structures de contrôle

## Conditions — if / else if / else

```cpp
#include <iostream>
using namespace std;

int main() {
    int note = 75;

    if (note >= 90) {
        cout << "Très bien !" << endl;
    } else if (note >= 70) {
        cout << "Bien" << endl;
    } else if (note >= 50) {
        cout << "Passable" << endl;
    } else {
        cout << "Insuffisant" << endl;
    }

    return 0;
}
```

### Opérateur ternaire

```cpp
int age = 20;
string statut = (age >= 18) ? "majeur" : "mineur";
cout << statut << endl; // "majeur"
```

---

## Switch

```cpp
int jour = 3;

switch (jour) {
    case 1:
        cout << "Lundi" << endl;
        break;
    case 2:
        cout << "Mardi" << endl;
        break;
    case 3:
        cout << "Mercredi" << endl;
        break;
    case 6:
    case 7:
        cout << "Week-end !" << endl;
        break;
    default:
        cout << "Autre jour" << endl;
}
```

> **Important :** sans `break`, l'exécution "tombe" dans le `case` suivant (fall-through).

---

## Boucle for

```cpp
// Syntaxe classique
for (initialisation; condition; incrément) {
    // code
}

// Compter de 0 à 4
for (int i = 0; i < 5; i++) {
    cout << i << " "; // 0 1 2 3 4
}
cout << endl;

// Compter à rebours
for (int i = 10; i > 0; i--) {
    cout << i << " "; // 10 9 8 7 6 5 4 3 2 1
}

// Incrément de 2
for (int i = 0; i <= 10; i += 2) {
    cout << i << " "; // 0 2 4 6 8 10
}
```

### Range-based for (C++11) — Pour les tableaux et conteneurs

```cpp
#include <vector>
using namespace std;

vector<int> nombres = {1, 2, 3, 4, 5};

for (int n : nombres) {
    cout << n << " "; // 1 2 3 4 5
}

// Avec référence (pour les grands objets, évite la copie)
for (const string& s : {"Alice", "Bob", "Charlie"}) {
    cout << s << endl;
}
```

---

## Boucle while

```cpp
int compteur = 0;

while (compteur < 5) {
    cout << compteur << " ";
    compteur++;
}
// 0 1 2 3 4

// Exemple : lire jusqu'à ce que l'utilisateur entre 0
int valeur;
cout << "Entrer des nombres (0 pour arrêter) : ";
while (cin >> valeur && valeur != 0) {
    cout << "Tu as entré : " << valeur << endl;
}
```

---

## Boucle do-while

La boucle `do-while` s'exécute **au moins une fois** avant de vérifier la condition.

```cpp
int x;

do {
    cout << "Entrer un nombre positif : ";
    cin >> x;
} while (x <= 0);

cout << "Tu as entré : " << x << endl;
```

---

## break et continue

```cpp
// break — quitter la boucle immédiatement
for (int i = 0; i < 10; i++) {
    if (i == 5) break;
    cout << i << " "; // 0 1 2 3 4
}

// continue — passer à l'itération suivante
for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) continue;  // Ignorer les pairs
    cout << i << " "; // 1 3 5 7 9
}
```

---

## Boucles imbriquées

```cpp
// Table de multiplication
for (int i = 1; i <= 5; i++) {
    for (int j = 1; j <= 5; j++) {
        cout << i * j << "\t";
    }
    cout << endl;
}
```

Sortie :
```
1   2   3   4   5
2   4   6   8   10
3   6   9   12  15
4   8   12  16  20
5   10  15  20  25
```

---

## Exemples pratiques

### Deviner un nombre

```cpp
#include <iostream>
#include <cstdlib>  // rand(), srand()
#include <ctime>    // time()
using namespace std;

int main() {
    srand(time(0));  // Initialiser le générateur aléatoire
    int secret = rand() % 100 + 1;  // Nombre entre 1 et 100
    int tentative;
    int essais = 0;

    cout << "Devine le nombre entre 1 et 100 !" << endl;

    do {
        cout << "Ta tentative : ";
        cin >> tentative;
        essais++;

        if (tentative < secret)
            cout << "C'est plus !" << endl;
        else if (tentative > secret)
            cout << "C'est moins !" << endl;
        else
            cout << "Bravo ! Trouvé en " << essais << " essais !" << endl;

    } while (tentative != secret);

    return 0;
}
```

### FizzBuzz (classique)

```cpp
for (int i = 1; i <= 100; i++) {
    if (i % 15 == 0)
        cout << "FizzBuzz" << endl;
    else if (i % 3 == 0)
        cout << "Fizz" << endl;
    else if (i % 5 == 0)
        cout << "Buzz" << endl;
    else
        cout << i << endl;
}
```

### Triangle d'étoiles

```cpp
int n = 5;
for (int i = 1; i <= n; i++) {
    for (int j = 0; j < i; j++) {
        cout << "* ";
    }
    cout << endl;
}
// *
// * *
// * * *
// * * * *
// * * * * *
```

---

## Exercices

1. **Calculer la somme** des entiers de 1 à N (N saisi par l'utilisateur)
2. **Vérifier si un nombre est premier** (divisible uniquement par 1 et lui-même)
3. **Suite de Fibonacci** : afficher les N premiers termes (0, 1, 1, 2, 3, 5, 8…)
4. **Menu interactif** : afficher un menu, lire le choix et effectuer une action

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| LearnCpp | [https://www.learncpp.com/cpp-tutorial/if-statements-and-blocks/](https://www.learncpp.com/cpp-tutorial/if-statements-and-blocks/) | Contrôle de flux |
| HackerRank C++ | [https://www.hackerrank.com/domains/cpp](https://www.hackerrank.com/domains/cpp) | Challenges C++ |

---

[← Variables](02-variables.md) | [Retour au README](../README.md) | [Fonctions →](04-fonctions.md)
