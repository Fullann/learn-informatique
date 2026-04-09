# Chapitre 07 — C++ : La STL (Standard Template Library)

## Qu'est-ce que la STL ?

La **STL** est la bibliothèque standard du C++. Elle fournit :
- Des **conteneurs** : structures de données prêtes à l'emploi
- Des **algorithmes** : tri, recherche, transformation…
- Des **itérateurs** : parcourir les conteneurs de façon uniforme

Grâce à la STL, tu n'as pas à réimplémenter des structures de données courantes.

---

## vector — Tableau dynamique

```cpp
#include <vector>
using namespace std;

vector<int> v = {3, 1, 4, 1, 5};
v.push_back(9);          // Ajouter à la fin
v.pop_back();            // Supprimer le dernier
v.insert(v.begin(), 0);  // Insérer au début
v.erase(v.begin() + 2);  // Supprimer à l'index 2
cout << v.size() << endl;
cout << v[0] << endl;
```

---

## string — Chaîne de caractères

```cpp
#include <string>
using namespace std;

string s = "Bonjour";
s += " monde";
cout << s.length() << endl;
cout << s.substr(0, 7) << endl;  // "Bonjour"
cout << s.find("monde") << endl; // 8
s.replace(0, 7, "Salut");        // "Salut monde"
```

---

## map — Dictionnaire (clé → valeur)

```cpp
#include <map>
using namespace std;

map<string, int> ages;
ages["Alice"] = 25;
ages["Bob"] = 30;
ages.insert({"Charlie", 22});

// Accès
cout << ages["Alice"] << endl;  // 25

// Vérifier l'existence
if (ages.count("Alice") > 0) {
    cout << "Alice existe !" << endl;
}
// ou
if (ages.find("Bob") != ages.end()) {
    cout << "Bob : " << ages["Bob"] << endl;
}

// Parcourir
for (auto& [nom, age] : ages) {  // C++17
    cout << nom << " : " << age << endl;
}

// Supprimer
ages.erase("Bob");
cout << ages.size() << endl;  // 2
```

---

## unordered_map — Dictionnaire sans ordre (plus rapide)

```cpp
#include <unordered_map>
using namespace std;

unordered_map<string, int> compteur;
vector<string> mots = {"chat", "chien", "chat", "oiseau", "chien", "chat"};

for (const string& mot : mots) {
    compteur[mot]++;  // Crée la clé si elle n'existe pas (valeur = 0)
}

for (auto& [mot, nb] : compteur) {
    cout << mot << " : " << nb << " fois" << endl;
}
// chat : 3, chien : 2, oiseau : 1
```

---

## set — Ensemble (éléments uniques, triés)

```cpp
#include <set>
using namespace std;

set<int> s = {5, 2, 8, 2, 1, 5, 3};
// Les doublons sont ignorés automatiquement !

for (int x : s) cout << x << " ";  // 1 2 3 5 8 (trié)

s.insert(7);
s.erase(2);
cout << s.count(5) << endl;  // 1 (présent)
cout << s.count(9) << endl;  // 0 (absent)
```

---

## stack — Pile (LIFO : Last In, First Out)

```cpp
#include <stack>
using namespace std;

stack<int> pile;
pile.push(1);
pile.push(2);
pile.push(3);

cout << pile.top() << endl;  // 3 (dernier entré)
pile.pop();                   // Enlever le dessus
cout << pile.top() << endl;  // 2
cout << pile.size() << endl; // 2
```

---

## queue — File (FIFO : First In, First Out)

```cpp
#include <queue>
using namespace std;

queue<string> file;
file.push("Alice");
file.push("Bob");
file.push("Charlie");

cout << file.front() << endl;  // "Alice" (premier entré)
file.pop();                     // Retirer le premier
cout << file.front() << endl;  // "Bob"
```

---

## pair et tuple

```cpp
#include <utility>  // pair
#include <tuple>    // tuple
using namespace std;

// pair — grouper 2 valeurs
pair<string, int> p = {"Alice", 25};
cout << p.first << " : " << p.second << endl;

// make_pair
auto p2 = make_pair("Bob", 30);

// tuple — grouper N valeurs
tuple<string, int, double> t = {"Alice", 25, 1.68};
cout << get<0>(t) << endl;  // "Alice"
cout << get<1>(t) << endl;  // 25
cout << get<2>(t) << endl;  // 1.68

// C++17 : structured bindings
auto [nom, age, taille] = t;
cout << nom << " " << age << endl;
```

---

## Les algorithmes (`<algorithm>`)

```cpp
#include <algorithm>
#include <vector>
using namespace std;

vector<int> v = {5, 2, 8, 1, 9, 3, 7};

// Tri
sort(v.begin(), v.end());                    // Croissant : {1,2,3,5,7,8,9}
sort(v.begin(), v.end(), greater<int>());    // Décroissant

// Recherche
auto it = find(v.begin(), v.end(), 5);
if (it != v.end()) cout << "Trouvé !" << endl;

// Recherche binaire (tableau trié requis)
bool trouve = binary_search(v.begin(), v.end(), 5); // true

// Min / Max
cout << *min_element(v.begin(), v.end()) << endl; // 1
cout << *max_element(v.begin(), v.end()) << endl; // 9

// Inverser
reverse(v.begin(), v.end());

// Compter
int nb = count(v.begin(), v.end(), 5); // Combien de 5 ?

// Transformer (map)
vector<int> doubles(v.size());
transform(v.begin(), v.end(), doubles.begin(), [](int x) { return x * 2; });

// Filtrer (copy_if)
vector<int> pairs;
copy_if(v.begin(), v.end(), back_inserter(pairs), [](int x) { return x % 2 == 0; });

// Somme (accumulate)
#include <numeric>
int somme = accumulate(v.begin(), v.end(), 0); // Somme de tous les éléments

// Pour chaque élément (for_each)
for_each(v.begin(), v.end(), [](int x) { cout << x << " "; });
```

---

## Itérateurs

Les itérateurs permettent de parcourir les conteneurs de manière uniforme.

```cpp
vector<int> v = {10, 20, 30, 40, 50};

// Itérateur classique
for (vector<int>::iterator it = v.begin(); it != v.end(); ++it) {
    cout << *it << " ";
}

// auto (plus simple)
for (auto it = v.begin(); it != v.end(); ++it) {
    cout << *it << " ";
}

// Itérateur inverse
for (auto it = v.rbegin(); it != v.rend(); ++it) {
    cout << *it << " ";  // 50 40 30 20 10
}
```

---

## Exemple complet — Gestionnaire de contacts

```cpp
#include <iostream>
#include <map>
#include <string>
#include <algorithm>
using namespace std;

class Contacts {
    map<string, string> annuaire;  // nom → numéro

public:
    void ajouter(string nom, string numero) {
        annuaire[nom] = numero;
        cout << nom << " ajouté." << endl;
    }

    void chercher(string nom) {
        auto it = annuaire.find(nom);
        if (it != annuaire.end()) {
            cout << nom << " : " << it->second << endl;
        } else {
            cout << nom << " non trouvé." << endl;
        }
    }

    void supprimer(string nom) {
        if (annuaire.erase(nom)) {
            cout << nom << " supprimé." << endl;
        } else {
            cout << nom << " non trouvé." << endl;
        }
    }

    void afficherTous() {
        cout << "\n--- Tous les contacts ---" << endl;
        for (auto& [nom, tel] : annuaire) {
            cout << nom << " : " << tel << endl;
        }
        cout << annuaire.size() << " contact(s)." << endl;
    }
};

int main() {
    Contacts c;
    c.ajouter("Alice", "06 12 34 56 78");
    c.ajouter("Bob", "07 98 76 54 32");
    c.ajouter("Charlie", "06 11 22 33 44");

    c.afficherTous();
    c.chercher("Bob");
    c.supprimer("Bob");
    c.chercher("Bob");

    return 0;
}
```

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| cppreference.com | [https://en.cppreference.com/w/cpp/container](https://en.cppreference.com/w/cpp/container) | Référence STL complète |
| LearnCpp STL | [https://www.learncpp.com/cpp-tutorial/the-standard-library/](https://www.learncpp.com/cpp-tutorial/the-standard-library/) | Introduction à la STL |
| HackerRank C++ | [https://www.hackerrank.com/domains/cpp](https://www.hackerrank.com/domains/cpp) | Exercices STL |

---

[← POO](06-poo.md) | [Retour au README](../README.md) | [Parcours C →](../c/01-introduction.md)
