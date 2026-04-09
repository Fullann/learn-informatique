# Chapitre 01 — C++ : Introduction

## Qu'est-ce que le C++ ?

Le **C++** est un langage de programmation compilé, créé par Bjarne Stroustrup en 1979 comme extension du C. Il est utilisé pour :

- Les jeux vidéo (Unreal Engine)
- Les systèmes d'exploitation et pilotes
- Les logiciels haute performance (navigateurs, bases de données)
- L'embarqué et la robotique
- La finance algorithmique

C++ combine la **puissance du C** (accès bas niveau à la mémoire) avec la **programmation orientée objet** (classes, héritage, polymorphisme).

---

## Installation

### Windows
1. Télécharger **MinGW-w64** : [https://winlibs.com/](https://winlibs.com/)
2. Ou installer **Visual Studio** (recommandé) : [https://visualstudio.microsoft.com/fr/](https://visualstudio.microsoft.com/fr/)
3. Ou utiliser **VS Code** + extension C/C++

### macOS
```bash
# Xcode Command Line Tools (installe g++ et clang++)
xcode-select --install
```

### Linux
```bash
sudo apt install g++ build-essential
```

### Vérifier l'installation
```bash
g++ --version
```

---

## Compiler et exécuter un programme

```bash
# Compiler
g++ main.cpp -o programme

# Exécuter
./programme        # Linux/macOS
programme.exe      # Windows

# Compiler avec les options recommandées
g++ -std=c++17 -Wall -Wextra -o programme main.cpp
```

---

## Ton premier programme

```cpp
#include <iostream>  // Bibliothèque d'entrée/sortie

int main() {
    std::cout << "Bonjour le monde !" << std::endl;
    return 0;
}
```

### Décorticage ligne par ligne

| Ligne | Signification |
|-------|---------------|
| `#include <iostream>` | Importer la bibliothèque I/O standard |
| `int main()` | Point d'entrée du programme (obligatoire) |
| `std::cout <<` | Afficher dans la console |
| `std::endl` | Retour à la ligne + vider le tampon |
| `return 0;` | Indiquer que le programme s'est terminé correctement |

---

## Éviter le préfixe std::

```cpp
#include <iostream>
using namespace std;  // Permet d'écrire cout au lieu de std::cout

int main() {
    cout << "Bonjour !" << endl;
    cout << "Une deuxième ligne." << endl;
    return 0;
}
```

> **Note :** `using namespace std;` est pratique pour apprendre, mais dans les grands projets on préfère garder le préfixe `std::` pour éviter les conflits de noms.

---

## Entrée utilisateur

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string prenom;
    int age;

    cout << "Quel est ton prénom ? ";
    cin >> prenom;

    cout << "Quel est ton âge ? ";
    cin >> age;

    cout << "Bonjour " << prenom << " ! Tu as " << age << " ans." << endl;

    return 0;
}
```

> `cin >>` lit jusqu'à l'espace. Pour lire une ligne entière :
> ```cpp
> getline(cin, prenom);
> ```

---

## Commentaires

```cpp
// Commentaire sur une ligne

/*
   Commentaire
   sur plusieurs
   lignes
*/
```

---

## Exercice — Calculatrice simple

Crée un programme qui :
1. Demande deux nombres à l'utilisateur
2. Affiche leur somme, différence, produit et quotient

```cpp
#include <iostream>
using namespace std;

int main() {
    double a, b;
    cout << "Entrer le premier nombre : ";
    cin >> a;
    cout << "Entrer le deuxième nombre : ";
    cin >> b;

    cout << "Somme     : " << a + b << endl;
    cout << "Différence: " << a - b << endl;
    cout << "Produit   : " << a * b << endl;
    if (b != 0)
        cout << "Quotient  : " << a / b << endl;
    else
        cout << "Division par zéro impossible." << endl;

    return 0;
}
```

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Compiler en ligne | [https://www.onlinegdb.com/online_c++_compiler](https://www.onlinegdb.com/online_c++_compiler) | Compiler C++ sans installation |
| Compiler en ligne 2 | [https://godbolt.org/](https://godbolt.org/) | Compiler Explorer |
| Exercism C++ | [https://exercism.org/tracks/cpp](https://exercism.org/tracks/cpp) | Exercices avec feedback |
| LearnCpp | [https://www.learncpp.com/](https://www.learncpp.com/) | Excellent tutoriel C++ en anglais |
| OpenClassrooms C++ | [https://openclassrooms.com/fr/courses/1894236-programmez-avec-le-langage-c](https://openclassrooms.com/fr/courses/1894236-programmez-avec-le-langage-c) | Cours en français |

---

[← Retour au README](../README.md) | [Chapitre suivant : Variables →](02-variables.md)
