# Chapitre 06 — C++ : Programmation Orientée Objet (POO)

## Qu'est-ce que la POO ?

La **Programmation Orientée Objet** organise le code autour d'**objets** qui regroupent des données (attributs) et des comportements (méthodes).

Les 4 piliers de la POO :
1. **Encapsulation** — Regrouper données et méthodes, contrôler l'accès
2. **Héritage** — Une classe peut hériter des propriétés d'une autre
3. **Polymorphisme** — Même interface, comportements différents
4. **Abstraction** — Cacher la complexité interne

---

## Classes et objets

```cpp
#include <iostream>
#include <string>
using namespace std;

// Définition d'une classe
class Personne {
public:
    // Attributs
    string prenom;
    string nom;
    int age;

    // Méthodes
    void sePresenter() {
        cout << "Bonjour, je suis " << prenom << " " << nom;
        cout << ", j'ai " << age << " ans." << endl;
    }

    bool estMajeur() {
        return age >= 18;
    }
};

int main() {
    // Créer un objet (instance de la classe)
    Personne alice;
    alice.prenom = "Alice";
    alice.nom = "Dupont";
    alice.age = 25;
    alice.sePresenter();  // "Bonjour, je suis Alice Dupont, j'ai 25 ans."

    Personne bob;
    bob.prenom = "Bob";
    bob.nom = "Martin";
    bob.age = 15;
    cout << bob.estMajeur() << endl; // 0 (false)

    return 0;
}
```

---

## Constructeur et destructeur

```cpp
class Rectangle {
public:
    double largeur;
    double hauteur;

    // Constructeur — appelé lors de la création de l'objet
    Rectangle(double l, double h) {
        largeur = l;
        hauteur = h;
        cout << "Rectangle créé !" << endl;
    }

    // Constructeur par défaut
    Rectangle() : largeur(1.0), hauteur(1.0) {}

    // Destructeur — appelé lors de la destruction de l'objet
    ~Rectangle() {
        cout << "Rectangle détruit." << endl;
    }

    double aire() {
        return largeur * hauteur;
    }

    double perimetre() {
        return 2 * (largeur + hauteur);
    }
};

int main() {
    Rectangle r1(5.0, 3.0);
    cout << "Aire : " << r1.aire() << endl;      // 15

    Rectangle r2;  // Constructeur par défaut
    cout << "Aire : " << r2.aire() << endl;      // 1

    return 0;
}  // Les destructeurs sont appelés automatiquement ici
```

### Liste d'initialisation (meilleure pratique)

```cpp
class Personne {
public:
    string prenom;
    int age;

    // Initialisation directe des membres (plus efficace)
    Personne(string p, int a) : prenom(p), age(a) {}
};
```

---

## Encapsulation — public, private, protected

```cpp
class CompteBancaire {
private:
    // Accessible uniquement dans la classe
    double solde;
    string numeroCompte;

public:
    // Interface publique
    CompteBancaire(string numero, double soldeInitial)
        : numeroCompte(numero), solde(soldeInitial) {}

    // Getters (accesseurs)
    double getSolde() const { return solde; }
    string getNumero() const { return numeroCompte; }

    // Méthodes publiques
    void deposer(double montant) {
        if (montant > 0) {
            solde += montant;
            cout << "Dépôt de " << montant << "€. Nouveau solde : " << solde << "€" << endl;
        }
    }

    bool retirer(double montant) {
        if (montant > 0 && montant <= solde) {
            solde -= montant;
            cout << "Retrait de " << montant << "€. Nouveau solde : " << solde << "€" << endl;
            return true;
        }
        cout << "Solde insuffisant !" << endl;
        return false;
    }
};

int main() {
    CompteBancaire compte("FR76001", 1000.0);
    // compte.solde = 9999; // ERREUR : solde est private
    compte.deposer(500);
    compte.retirer(200);
    cout << "Solde : " << compte.getSolde() << "€" << endl;
    return 0;
}
```

---

## Héritage

Une classe peut **hériter** des attributs et méthodes d'une autre classe.

```cpp
// Classe de base (parent)
class Animal {
protected:
    string nom;
    int age;

public:
    Animal(string n, int a) : nom(n), age(a) {}

    void sePresenter() {
        cout << "Je suis " << nom << ", j'ai " << age << " ans." << endl;
    }

    virtual void faireDuBruit() {
        cout << "..." << endl;
    }
};

// Classes dérivées (enfants)
class Chien : public Animal {
private:
    string race;

public:
    Chien(string n, int a, string r) : Animal(n, a), race(r) {}

    void faireDuBruit() override {
        cout << nom << " : Ouaf Ouaf !" << endl;
    }

    void chercher() {
        cout << nom << " rapporte la balle !" << endl;
    }
};

class Chat : public Animal {
public:
    Chat(string n, int a) : Animal(n, a) {}

    void faireDuBruit() override {
        cout << nom << " : Miaou !" << endl;
    }
};

int main() {
    Chien rex("Rex", 3, "Labrador");
    rex.sePresenter();       // Hérité de Animal
    rex.faireDuBruit();     // Ouaf Ouaf !
    rex.chercher();

    Chat mimi("Mimi", 5);
    mimi.sePresenter();
    mimi.faireDuBruit();    // Miaou !

    return 0;
}
```

---

## Polymorphisme

Le polymorphisme permet d'utiliser des objets de différents types via une **interface commune**.

```cpp
#include <vector>

// Utilisation de pointeurs vers la classe de base
vector<Animal*> animaux;
animaux.push_back(new Chien("Rex", 3, "Labrador"));
animaux.push_back(new Chat("Mimi", 5));
animaux.push_back(new Chien("Buddy", 2, "Beagle"));

// Appel polymorphique : le bon faireDuBruit() est appelé
for (Animal* a : animaux) {
    a->faireDuBruit();
}

// Libérer la mémoire
for (Animal* a : animaux) {
    delete a;
}
```

Sortie :
```
Rex : Ouaf Ouaf !
Mimi : Miaou !
Buddy : Ouaf Ouaf !
```

---

## Classes abstraites et méthodes virtuelles pures

```cpp
class Forme {
public:
    string couleur;

    Forme(string c) : couleur(c) {}

    // Méthode virtuelle pure — doit être implémentée par les sous-classes
    virtual double aire() = 0;
    virtual double perimetre() = 0;

    virtual void afficher() {
        cout << "Forme " << couleur << " - Aire : " << aire() << endl;
    }
};

// Forme est abstraite, on ne peut pas faire : Forme f;

class Cercle : public Forme {
    double rayon;
public:
    Cercle(string c, double r) : Forme(c), rayon(r) {}

    double aire() override {
        return 3.14159 * rayon * rayon;
    }

    double perimetre() override {
        return 2 * 3.14159 * rayon;
    }
};

class Rectangle : public Forme {
    double largeur, hauteur;
public:
    Rectangle(string c, double l, double h) : Forme(c), largeur(l), hauteur(h) {}

    double aire() override { return largeur * hauteur; }
    double perimetre() override { return 2 * (largeur + hauteur); }
};
```

---

## Exemple complet — Système de gestion

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class Etudiant {
private:
    string nom;
    vector<double> notes;

public:
    Etudiant(string n) : nom(n) {}

    void ajouterNote(double note) {
        if (note >= 0 && note <= 20) {
            notes.push_back(note);
        }
    }

    double moyenne() const {
        if (notes.empty()) return 0;
        double somme = 0;
        for (double n : notes) somme += n;
        return somme / notes.size();
    }

    string getNom() const { return nom; }

    void afficher() const {
        cout << "Étudiant : " << nom << " | Moyenne : " << moyenne();
        cout << "/20 | " << (moyenne() >= 10 ? "RÉUSSI" : "ÉCHOUÉ") << endl;
    }
};

class Classe {
private:
    string nom;
    vector<Etudiant> etudiants;

public:
    Classe(string n) : nom(n) {}

    void ajouterEtudiant(Etudiant& e) {
        etudiants.push_back(e);
    }

    void afficherResultats() {
        cout << "\n=== Résultats de la classe " << nom << " ===" << endl;
        for (auto& e : etudiants) e.afficher();
    }

    double moyenneClasse() {
        if (etudiants.empty()) return 0;
        double somme = 0;
        for (auto& e : etudiants) somme += e.moyenne();
        return somme / etudiants.size();
    }
};

int main() {
    Etudiant alice("Alice");
    alice.ajouterNote(15);
    alice.ajouterNote(18);
    alice.ajouterNote(12);

    Etudiant bob("Bob");
    bob.ajouterNote(8);
    bob.ajouterNote(11);
    bob.ajouterNote(9);

    Classe cp("Terminale");
    cp.ajouterEtudiant(alice);
    cp.ajouterEtudiant(bob);
    cp.afficherResultats();

    cout << "Moyenne de la classe : " << cp.moyenneClasse() << "/20" << endl;

    return 0;
}
```

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| LearnCpp POO | [https://www.learncpp.com/cpp-tutorial/welcome-to-object-oriented-programming/](https://www.learncpp.com/cpp-tutorial/welcome-to-object-oriented-programming/) | Cours POO complet |
| Exercism C++ | [https://exercism.org/tracks/cpp](https://exercism.org/tracks/cpp) | Exercices pratiques |

---

[← Tableaux/Pointeurs](05-tableaux-pointeurs.md) | [Retour au README](../README.md) | [STL →](07-stl.md)
