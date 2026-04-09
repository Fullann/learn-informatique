# Chapitre 02 — C++ : Variables et types

## Les types fondamentaux

En C++, chaque variable a un **type précis** qui détermine :
- La taille en mémoire
- Les valeurs possibles
- Les opérations autorisées

```cpp
#include <iostream>
using namespace std;

int main() {
    // Entiers
    int age = 25;                  // -2 147 483 648 à 2 147 483 647 (4 octets)
    short petitNombre = 100;       // -32 768 à 32 767 (2 octets)
    long grandNombre = 1000000L;   // (4 ou 8 octets)
    long long tresGrand = 9000000000LL; // (8 octets)
    unsigned int positif = 42;     // 0 à 4 294 967 295

    // Décimaux
    float prix = 3.14f;            // ~7 chiffres significatifs (4 octets)
    double precision = 3.14159265; // ~15 chiffres significatifs (8 octets)

    // Caractère
    char lettre = 'A';             // Un seul caractère (1 octet)

    // Booléen
    bool estVrai = true;           // true ou false (1 octet)

    // Chaîne (nécessite <string>)
    string prenom = "Alice";

    return 0;
}
```

---

## Tailles des types

```cpp
#include <iostream>
using namespace std;

int main() {
    cout << "int    : " << sizeof(int)    << " octets" << endl;  // 4
    cout << "float  : " << sizeof(float)  << " octets" << endl;  // 4
    cout << "double : " << sizeof(double) << " octets" << endl;  // 8
    cout << "char   : " << sizeof(char)   << " octet"  << endl;  // 1
    cout << "bool   : " << sizeof(bool)   << " octet"  << endl;  // 1
    return 0;
}
```

---

## Déclaration et initialisation

```cpp
// Déclaration sans valeur (non recommandé, valeur indéfinie)
int x;

// Déclaration avec valeur
int y = 10;
int z(10);      // Syntaxe alternative
int w{10};      // Initialisation uniforme (C++11, recommandé)

// Constante — ne peut pas être modifiée
const double PI = 3.14159265;
// PI = 3.0; // ERREUR de compilation

// auto — le compilateur déduit le type (C++11)
auto nombre = 42;       // int
auto prix = 3.14;       // double
auto lettre = 'A';      // char
auto texte = string("Bonjour"); // string
```

---

## Opérateurs arithmétiques

```cpp
#include <iostream>
using namespace std;

int main() {
    int a = 10, b = 3;

    cout << a + b << endl;   // 13
    cout << a - b << endl;   // 7
    cout << a * b << endl;   // 30
    cout << a / b << endl;   // 3 (division entière !)
    cout << a % b << endl;   // 1 (modulo = reste)

    // Division flottante
    double c = 10.0, d = 3.0;
    cout << c / d << endl;   // 3.33333...

    // Cast pour obtenir la division flottante
    cout << (double)a / b << endl; // 3.33333...

    // Puissance (besoin de <cmath>)
    #include <cmath>
    cout << pow(2, 8) << endl; // 256

    return 0;
}
```

---

## Opérateurs d'assignation

```cpp
int x = 10;
x += 5;   // x = 15
x -= 3;   // x = 12
x *= 2;   // x = 24
x /= 4;   // x = 6
x %= 4;   // x = 2
x++;      // x = 3 (post-incrémentation)
++x;      // x = 4 (pré-incrémentation)
x--;      // x = 3
--x;      // x = 2
```

---

## Opérateurs de comparaison et logiques

```cpp
int a = 5, b = 10;

// Comparaison (retourne bool)
a == b    // false
a != b    // true
a < b     // true
a > b     // false
a <= b    // true
a >= b    // false

// Logique
true && false   // false (ET)
true || false   // true  (OU)
!true           // false (NON)

// Exemple
bool adulte = (age >= 18) && (age < 65);
```

---

## Conversion de types (casting)

```cpp
int entier = 7;
double decimal = (double)entier; // Cast C-style
double decimal2 = static_cast<double>(entier); // Cast C++ (recommandé)

// Attention aux pertes de précision
double pi = 3.14159;
int piEntier = (int)pi; // 3 (partie décimale perdue)

// Conversion string <-> nombre
#include <string>
string s = to_string(42);        // int → string
int n = stoi("42");              // string → int
double d = stod("3.14");         // string → double
```

---

## Les chaînes de caractères (string)

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string prenom = "Alice";
    string nom = "Dupont";

    // Concaténation
    string nomComplet = prenom + " " + nom;
    cout << nomComplet << endl; // "Alice Dupont"

    // Longueur
    cout << prenom.length() << endl; // 5
    cout << prenom.size() << endl;   // 5 (identique)

    // Accès à un caractère
    cout << prenom[0] << endl;    // 'A'
    cout << prenom.at(1) << endl; // 'l' (avec vérification des bornes)

    // Sous-chaîne
    cout << nomComplet.substr(6, 6) << endl; // "Dupont"

    // Trouver une sous-chaîne
    size_t pos = nomComplet.find("Dupont");
    if (pos != string::npos) {
        cout << "Trouvé à la position : " << pos << endl;
    }

    // Remplacer
    string s = "Bonjour monde";
    s.replace(s.find("monde"), 5, "C++");
    cout << s << endl; // "Bonjour C++"

    // Comparer
    if (prenom == "Alice") cout << "C'est Alice !" << endl;

    // Vider
    prenom.clear();
    cout << prenom.empty() << endl; // 1 (true)

    return 0;
}
```

---

## Saisie et affichage formaté

```cpp
#include <iostream>
#include <iomanip>  // Pour le formatage
using namespace std;

int main() {
    double prix = 1234.5678;

    // Précision décimale
    cout << fixed << setprecision(2) << prix << endl; // 1234.57

    // Largeur minimale
    cout << setw(10) << "texte" << endl; // "     texte"

    // Alignement à gauche
    cout << left << setw(10) << "texte" << endl; // "texte     "

    return 0;
}
```

---

## Exercice

Écris un programme qui :
1. Demande le nom, l'âge et la taille (en cm) d'un utilisateur
2. Calcule et affiche son IMC (poids en kg à demander aussi) : `IMC = poids / (taille_m * taille_m)`
3. Affiche le résultat avec 2 décimales et l'interprétation (insuffisant, normal, surpoids)

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| LearnCpp | [https://www.learncpp.com/cpp-tutorial/introduction-to-variables/](https://www.learncpp.com/cpp-tutorial/introduction-to-variables/) | Variables et types |
| Exercism C++ | [https://exercism.org/tracks/cpp](https://exercism.org/tracks/cpp) | Exercices pratiques |

---

[← Introduction](01-introduction.md) | [Retour au README](../README.md) | [Structures de contrôle →](03-controle.md)
