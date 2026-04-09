# Chapitre 04 — C++ : Fonctions

## Pourquoi utiliser des fonctions ?

Les fonctions permettent de :
- **Découper** le programme en blocs réutilisables
- **Éviter la répétition** (DRY : Don't Repeat Yourself)
- **Faciliter la lecture** et la maintenance du code

---

## Syntaxe de base

```cpp
type_retour nomFonction(type param1, type param2) {
    // Corps de la fonction
    return valeur;
}
```

```cpp
#include <iostream>
using namespace std;

// Déclaration de la fonction
int additionner(int a, int b) {
    return a + b;
}

int main() {
    int resultat = additionner(3, 7);
    cout << resultat << endl; // 10
    return 0;
}
```

---

## Type de retour void

Si la fonction ne retourne rien, utiliser `void`.

```cpp
void afficherBonjour(string nom) {
    cout << "Bonjour " << nom << " !" << endl;
    // Pas de return nécessaire
}

afficherBonjour("Alice"); // "Bonjour Alice !"
```

---

## Prototypes de fonctions

En C++, une fonction doit être **déclarée avant d'être utilisée**. Si tu veux définir `main()` en premier, tu peux utiliser un **prototype**.

```cpp
#include <iostream>
using namespace std;

// Prototype (déclaration)
int additionner(int a, int b);
void afficher(string message);

int main() {
    cout << additionner(5, 3) << endl;
    afficher("Bonjour !");
    return 0;
}

// Définition
int additionner(int a, int b) {
    return a + b;
}

void afficher(string message) {
    cout << message << endl;
}
```

---

## Paramètres par défaut

```cpp
void saluer(string nom, string salutation = "Bonjour") {
    cout << salutation << " " << nom << " !" << endl;
}

saluer("Alice");             // "Bonjour Alice !"
saluer("Bob", "Bonsoir");    // "Bonsoir Bob !"
```

> Les paramètres avec valeur par défaut doivent être en **dernier**.

---

## Passage par valeur vs par référence

### Par valeur (copie)

```cpp
void doubler(int x) {
    x = x * 2;  // Modifie la copie, pas l'original
}

int a = 5;
doubler(a);
cout << a << endl; // 5 (inchangé)
```

### Par référence (&)

```cpp
void doubler(int& x) {
    x = x * 2;  // Modifie l'original
}

int a = 5;
doubler(a);
cout << a << endl; // 10 (modifié)
```

### Par référence constante (pour lecture seule)

```cpp
// Efficace pour les grands objets : pas de copie, mais pas de modification
void afficher(const string& texte) {
    cout << texte << endl;
    // texte = "modifié"; // ERREUR
}
```

---

## Surcharge de fonctions (overloading)

En C++, plusieurs fonctions peuvent avoir le **même nom** si leurs paramètres diffèrent.

```cpp
int additionner(int a, int b) {
    return a + b;
}

double additionner(double a, double b) {
    return a + b;
}

string additionner(string a, string b) {
    return a + b;
}

cout << additionner(3, 4) << endl;        // 7
cout << additionner(3.14, 2.71) << endl;  // 5.85
cout << additionner("Bon", "jour") << endl; // "Bonjour"
```

---

## Fonctions récursives

Une fonction peut s'appeler elle-même. C'est la **récursivité**.

```cpp
// Factorielle : n! = n * (n-1) * ... * 2 * 1
int factorielle(int n) {
    if (n <= 1) return 1;  // Cas de base
    return n * factorielle(n - 1);  // Appel récursif
}

cout << factorielle(5) << endl; // 120 (5*4*3*2*1)

// Fibonacci
int fibonacci(int n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
}

cout << fibonacci(10) << endl; // 55
```

---

## Fonctions inline

Pour les petites fonctions très appelées, `inline` suggère au compilateur de remplacer l'appel par le code directement (optimisation).

```cpp
inline int max(int a, int b) {
    return (a > b) ? a : b;
}
```

---

## Fonctions lambda (C++11)

Les fonctions lambda sont des **fonctions anonymes** définies là où elles sont utilisées.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    // Lambda simple
    auto saluer = [](string nom) {
        cout << "Bonjour " << nom << endl;
    };
    saluer("Alice");

    // Lambda qui capture une variable locale
    int seuil = 5;
    auto estGrand = [seuil](int x) {
        return x > seuil;
    };
    cout << estGrand(8) << endl; // 1 (true)
    cout << estGrand(3) << endl; // 0 (false)

    // Utilisation avec les algorithmes
    vector<int> nombres = {3, 1, 4, 1, 5, 9, 2, 6};
    sort(nombres.begin(), nombres.end(), [](int a, int b) {
        return a > b;  // Tri décroissant
    });
    for (int n : nombres) cout << n << " "; // 9 6 5 4 3 2 1 1
    cout << endl;

    return 0;
}
```

---

## Exemple complet — Calculatrice

```cpp
#include <iostream>
using namespace std;

double additionner(double a, double b) { return a + b; }
double soustraire(double a, double b) { return a - b; }
double multiplier(double a, double b) { return a * b; }
double diviser(double a, double b) {
    if (b == 0) {
        cout << "Erreur : division par zéro !" << endl;
        return 0;
    }
    return a / b;
}

void afficherMenu() {
    cout << "\n--- Calculatrice ---" << endl;
    cout << "1. Addition" << endl;
    cout << "2. Soustraction" << endl;
    cout << "3. Multiplication" << endl;
    cout << "4. Division" << endl;
    cout << "5. Quitter" << endl;
    cout << "Ton choix : ";
}

int main() {
    int choix;
    double a, b;

    do {
        afficherMenu();
        cin >> choix;

        if (choix >= 1 && choix <= 4) {
            cout << "Premier nombre : ";
            cin >> a;
            cout << "Deuxième nombre : ";
            cin >> b;
        }

        switch (choix) {
            case 1: cout << "Résultat : " << additionner(a, b) << endl; break;
            case 2: cout << "Résultat : " << soustraire(a, b) << endl; break;
            case 3: cout << "Résultat : " << multiplier(a, b) << endl; break;
            case 4: cout << "Résultat : " << diviser(a, b)    << endl; break;
            case 5: cout << "Au revoir !" << endl; break;
            default: cout << "Choix invalide." << endl;
        }
    } while (choix != 5);

    return 0;
}
```

---

## Exercices

1. Écrire une fonction `estPremier(int n)` qui retourne `true` si n est premier
2. Écrire une fonction `inverserChaine(string s)` qui retourne la chaîne inversée
3. Écrire une fonction `puissance(double base, int exposant)` sans utiliser `pow()`
4. Écrire une fonction `compterOccurrences(string texte, char c)` qui compte les apparitions de c

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| LearnCpp | [https://www.learncpp.com/cpp-tutorial/introduction-to-functions/](https://www.learncpp.com/cpp-tutorial/introduction-to-functions/) | Cours sur les fonctions |
| Exercism C++ | [https://exercism.org/tracks/cpp](https://exercism.org/tracks/cpp) | Exercices avec corrections |

---

[← Structures de contrôle](03-controle.md) | [Retour au README](../README.md) | [Tableaux et pointeurs →](05-tableaux-pointeurs.md)
