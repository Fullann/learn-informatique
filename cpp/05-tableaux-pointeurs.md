# Chapitre 05 — C++ : Tableaux et Pointeurs

## Tableaux statiques

Un tableau est une **séquence d'éléments du même type** en mémoire contiguë.

```cpp
#include <iostream>
using namespace std;

int main() {
    // Déclaration
    int notes[5];                          // 5 entiers non initialisés
    int scores[5] = {10, 20, 30, 40, 50}; // Initialisé
    int auto_size[] = {1, 2, 3, 4};       // Taille déduite automatiquement

    // Accès (index commence à 0)
    cout << scores[0] << endl; // 10
    cout << scores[4] << endl; // 50

    // Modifier
    scores[2] = 99;

    // Taille
    int taille = sizeof(scores) / sizeof(scores[0]); // 5

    // Parcourir
    for (int i = 0; i < 5; i++) {
        cout << scores[i] << " ";
    }
    cout << endl;

    // Range-based for (C++11)
    for (int s : scores) {
        cout << s << " ";
    }

    return 0;
}
```

> **Attention :** les tableaux statiques n'ont pas de vérification des bornes. Accéder à `scores[10]` = comportement indéfini !

---

## Tableaux 2D

```cpp
int grille[3][3] = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

cout << grille[1][2] << endl; // 6 (ligne 1, colonne 2)

// Parcourir
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        cout << grille[i][j] << " ";
    }
    cout << endl;
}
```

---

## std::array (C++11) — Alternative sécurisée

```cpp
#include <array>
using namespace std;

array<int, 5> nombres = {10, 20, 30, 40, 50};

cout << nombres.size() << endl;  // 5
cout << nombres[2] << endl;      // 30
cout << nombres.at(10) << endl;  // Lance une exception si hors bornes

// Itérer
for (int n : nombres) {
    cout << n << " ";
}
```

---

## Les pointeurs

Un **pointeur** est une variable qui stocke l'**adresse mémoire** d'une autre variable.

```
Variable a :  [  42  ]  ← stocke une valeur
              adresse : 0x7ffd1234

Pointeur p :  [0x7ffd1234]  ← stocke une adresse
```

```cpp
#include <iostream>
using namespace std;

int main() {
    int a = 42;

    // & = opérateur "adresse de"
    cout << "Valeur de a : " << a << endl;
    cout << "Adresse de a : " << &a << endl;  // ex: 0x7ffd1234

    // Déclarer un pointeur
    int* p = &a;  // p pointe vers a

    // * = opérateur "déréférencement" (accéder à la valeur pointée)
    cout << "Pointeur p : " << p << endl;     // Adresse de a
    cout << "Valeur via p : " << *p << endl;  // 42

    // Modifier via le pointeur
    *p = 100;
    cout << "a après modification : " << a << endl; // 100

    // Pointeur nul
    int* ptr = nullptr;  // Pointeur vide (ne pointe nulle part)

    return 0;
}
```

---

## Pointeurs et tableaux

En C++, le nom d'un tableau est un pointeur vers son premier élément.

```cpp
int tab[] = {10, 20, 30, 40, 50};
int* p = tab;  // Équivalent à int* p = &tab[0]

cout << *p << endl;       // 10
cout << *(p + 1) << endl; // 20
cout << *(p + 2) << endl; // 30

// Arithmétique de pointeurs
p++;            // p pointe maintenant sur tab[1]
cout << *p << endl; // 20

// Équivalent : tab[i] == *(tab + i)
cout << tab[3] << endl;     // 40
cout << *(tab + 3) << endl; // 40
```

---

## Passer des tableaux aux fonctions

```cpp
// Les tableaux sont passés comme pointeurs
void afficher(int* tab, int taille) {
    for (int i = 0; i < taille; i++) {
        cout << tab[i] << " ";
    }
    cout << endl;
}

// Syntaxe alternative
void afficher2(int tab[], int taille) {
    // identique
}

int scores[] = {5, 3, 8, 1, 9};
afficher(scores, 5);
```

---

## Allocation dynamique de mémoire

Parfois on ne connaît pas la taille d'un tableau à l'avance. On utilise `new` et `delete`.

```cpp
#include <iostream>
using namespace std;

int main() {
    int n;
    cout << "Combien de nombres ? ";
    cin >> n;

    // Allouer un tableau dynamiquement
    int* tableau = new int[n];

    for (int i = 0; i < n; i++) {
        cout << "Entrer le nombre " << (i+1) << " : ";
        cin >> tableau[i];
    }

    cout << "Tu as entré : ";
    for (int i = 0; i < n; i++) {
        cout << tableau[i] << " ";
    }
    cout << endl;

    // IMPORTANT : libérer la mémoire
    delete[] tableau;

    return 0;
}
```

> En pratique moderne, on préfère `std::vector` à la gestion manuelle de mémoire.

---

## std::vector — Le tableau dynamique recommandé

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> v;              // Vecteur vide
    vector<int> v2 = {1, 2, 3}; // Initialisé
    vector<int> v3(5, 0);       // 5 zéros

    // Ajouter
    v.push_back(10);
    v.push_back(20);
    v.push_back(30);

    // Accès
    cout << v[0] << endl;        // 10
    cout << v.at(1) << endl;     // 20
    cout << v.front() << endl;   // 10 (premier)
    cout << v.back() << endl;    // 30 (dernier)

    // Taille
    cout << v.size() << endl;    // 3

    // Supprimer le dernier
    v.pop_back();

    // Insérer à une position
    v.insert(v.begin() + 1, 15); // {10, 15, 20}

    // Supprimer à une position
    v.erase(v.begin());          // {15, 20}

    // Vider
    v.clear();
    cout << v.empty() << endl;   // 1 (true)

    // Trier
    vector<int> nums = {5, 2, 8, 1, 9, 3};
    sort(nums.begin(), nums.end());  // Croissant
    sort(nums.begin(), nums.end(), greater<int>()); // Décroissant

    // Chercher
    auto it = find(nums.begin(), nums.end(), 8);
    if (it != nums.end()) {
        cout << "Trouvé à l'index : " << (it - nums.begin()) << endl;
    }

    return 0;
}
```

---

## Références vs Pointeurs

| | Référence `&` | Pointeur `*` |
|---|---|---|
| Peut être null | Non | Oui (`nullptr`) |
| Peut être réassigné | Non | Oui |
| Syntaxe d'accès | Directe (`x`) | Déréférencement (`*p`) |
| Usage typique | Paramètres de fonctions | Mémoire dynamique, tableaux |

```cpp
// Référence
int a = 10;
int& ref = a;  // ref EST a (alias)
ref = 20;
cout << a << endl; // 20

// Pointeur
int* ptr = &a;
*ptr = 30;
cout << a << endl; // 30
```

---

## Exercices

1. Écrire une fonction qui trouve le **minimum et maximum** d'un tableau
2. Écrire une fonction qui **inverse** un tableau (sans en créer un nouveau)
3. Écrire une fonction qui **trie** un tableau par sélection (selection sort)
4. Utiliser un `vector` pour stocker des noms et les afficher triés

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| LearnCpp Pointers | [https://www.learncpp.com/cpp-tutorial/introduction-to-pointers/](https://www.learncpp.com/cpp-tutorial/introduction-to-pointers/) | Cours détaillé sur les pointeurs |
| LearnCpp Vectors | [https://www.learncpp.com/cpp-tutorial/introduction-to-stdvector/](https://www.learncpp.com/cpp-tutorial/introduction-to-stdvector/) | Cours sur les vecteurs |

---

[← Fonctions](04-fonctions.md) | [Retour au README](../README.md) | [POO →](06-poo.md)
