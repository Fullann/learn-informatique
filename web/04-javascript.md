# Chapitre 04 — JavaScript

## Qu'est-ce que JavaScript ?

**JavaScript (JS)** est le seul langage de programmation natif des navigateurs web. Il permet de rendre les pages **interactives** : réagir aux clics, modifier le contenu, faire des animations, communiquer avec un serveur, etc.

JavaScript est également utilisé côté serveur (Node.js), en mobile (React Native), et en desktop (Electron).

---

## Intégrer JS dans une page HTML

```html
<!-- En bas du <body> (recommandé) -->
<script src="script.js"></script>

<!-- Ou directement dans le HTML -->
<script>
  console.log("Bonjour !");
</script>
```

---

## Les bases du langage

### Variables
```js
// let — variable modifiable (recommandé)
let age = 25;
age = 26; // OK

// const — constante (non modifiable)
const nom = "Alice";
// nom = "Bob"; // ERREUR

// var — ancienne syntaxe (éviter)
var vieux = true;
```

### Types de données
```js
let texte = "Bonjour";       // String (chaîne)
let nombre = 42;              // Number
let decimal = 3.14;           // Number (décimal)
let vrai = true;              // Boolean
let rien = null;              // Null (volontairement vide)
let indefini = undefined;     // Undefined (pas encore assigné)

// Vérifier le type
console.log(typeof texte);    // "string"
console.log(typeof nombre);   // "number"
console.log(typeof vrai);     // "boolean"
```

### Opérateurs
```js
// Arithmétique
let a = 10 + 3;   // 13
let b = 10 - 3;   // 7
let c = 10 * 3;   // 30
let d = 10 / 3;   // 3.333...
let e = 10 % 3;   // 1 (reste de la division)
let f = 2 ** 3;   // 8 (puissance)

// Comparaison (retourne true ou false)
5 == "5"    // true  (comparaison de valeur, éviter)
5 === "5"   // false (comparaison stricte, valeur + type)
5 !== 6     // true
5 > 3       // true
5 <= 5      // true

// Logique
true && false  // false (ET)
true || false  // true  (OU)
!true          // false (NON)

// Assignation raccourcie
let x = 10;
x += 5;  // x = 15
x -= 3;  // x = 12
x *= 2;  // x = 24
x++;     // x = 25
x--;     // x = 24
```

### Chaînes de caractères
```js
let prenom = "Alice";
let nom = "Dupont";

// Concaténation
let phrase = "Bonjour " + prenom + " " + nom;

// Template literals (recommandé)
let phrase2 = `Bonjour ${prenom} ${nom} !`;

// Méthodes utiles
"hello".toUpperCase()        // "HELLO"
"WORLD".toLowerCase()        // "world"
"  bonjour  ".trim()         // "bonjour"
"hello world".includes("world")  // true
"hello".replace("hello", "salut")  // "salut"
"a,b,c".split(",")           // ["a", "b", "c"]
"bonjour".length             // 7
"bonjour"[0]                 // "b"
```

---

## Structures de contrôle

### Conditions
```js
let age = 18;

if (age >= 18) {
  console.log("Majeur");
} else if (age >= 13) {
  console.log("Adolescent");
} else {
  console.log("Enfant");
}

// Opérateur ternaire
let statut = age >= 18 ? "majeur" : "mineur";

// Switch
let jour = "lundi";
switch (jour) {
  case "lundi":
    console.log("Début de semaine");
    break;
  case "vendredi":
    console.log("Fin de semaine !");
    break;
  default:
    console.log("Jour normal");
}
```

### Boucles
```js
// for
for (let i = 0; i < 5; i++) {
  console.log(i); // 0 1 2 3 4
}

// while
let compteur = 0;
while (compteur < 3) {
  console.log(compteur);
  compteur++;
}

// for...of (pour les tableaux)
let fruits = ["pomme", "banane", "cerise"];
for (let fruit of fruits) {
  console.log(fruit);
}
```

---

## Fonctions

```js
// Déclaration classique
function additionner(a, b) {
  return a + b;
}
let resultat = additionner(3, 4); // 7

// Fonction fléchée (arrow function)
const multiplier = (a, b) => a * b;
const saluer = nom => `Bonjour ${nom}`;
const direBonjour = () => console.log("Bonjour !");

// Paramètres par défaut
function salutation(nom = "invité") {
  return `Bonjour ${nom}`;
}
salutation();         // "Bonjour invité"
salutation("Alice");  // "Bonjour Alice"
```

---

## Tableaux (Arrays)

```js
let fruits = ["pomme", "banane", "cerise"];

// Accès
fruits[0]           // "pomme"
fruits.length       // 3

// Modifier
fruits.push("kiwi");        // Ajouter à la fin
fruits.pop();               // Supprimer le dernier
fruits.unshift("fraise");   // Ajouter au début
fruits.shift();             // Supprimer le premier

// Méthodes fonctionnelles
let nombres = [1, 2, 3, 4, 5];

// map — transformer chaque élément
let doubles = nombres.map(n => n * 2);       // [2, 4, 6, 8, 10]

// filter — garder certains éléments
let pairs = nombres.filter(n => n % 2 === 0); // [2, 4]

// find — trouver un élément
let premierPair = nombres.find(n => n % 2 === 0); // 2

// reduce — réduire en une valeur
let somme = nombres.reduce((acc, n) => acc + n, 0); // 15

// forEach — itérer (sans retour)
nombres.forEach(n => console.log(n));

// includes — vérifier la présence
nombres.includes(3); // true

// indexOf — trouver la position
nombres.indexOf(3);  // 2

// join — transformer en chaîne
["a", "b", "c"].join("-"); // "a-b-c"
```

---

## Objets

```js
let personne = {
  prenom: "Alice",
  nom: "Dupont",
  age: 25,
  parler: function() {
    return `Je suis ${this.prenom}`;
  }
};

// Accès aux propriétés
personne.prenom     // "Alice"
personne["age"]     // 25
personne.parler()   // "Je suis Alice"

// Modifier
personne.age = 26;
personne.email = "alice@email.com"; // Ajouter

// Déstructuration
const { prenom, age } = personne;
console.log(prenom); // "Alice"

// Spread operator
const copie = { ...personne, age: 30 };
```

---

## Manipulation du DOM

Le **DOM** (Document Object Model) représente la page HTML sous forme d'arbre d'objets que JS peut modifier.

```js
// Sélectionner des éléments
const titre = document.getElementById("titre");
const premier = document.querySelector(".classe");      // Premier match
const tous = document.querySelectorAll(".classe");      // Tous les matches

// Modifier le contenu
titre.textContent = "Nouveau titre";
titre.innerHTML = "<strong>Titre gras</strong>";

// Modifier le style
titre.style.color = "red";
titre.style.fontSize = "24px";

// Modifier les classes
titre.classList.add("active");
titre.classList.remove("hidden");
titre.classList.toggle("visible");
titre.classList.contains("active"); // true/false

// Modifier les attributs
const lien = document.querySelector("a");
lien.setAttribute("href", "https://example.com");
lien.getAttribute("href");

// Créer et insérer des éléments
const newDiv = document.createElement("div");
newDiv.textContent = "Nouveau div";
document.body.appendChild(newDiv);

// Supprimer un élément
newDiv.remove();
```

---

## Événements

```js
const btn = document.querySelector("#monBouton");

// Écouter un événement
btn.addEventListener("click", function() {
  alert("Bouton cliqué !");
});

// Avec arrow function
btn.addEventListener("click", () => {
  console.log("Cliqué !");
});

// Événements courants
element.addEventListener("click", handler);         // Clic
element.addEventListener("mouseover", handler);     // Survol
element.addEventListener("keydown", handler);       // Touche pressée
element.addEventListener("submit", handler);        // Formulaire soumis
element.addEventListener("input", handler);         // Saisie dans un champ
element.addEventListener("DOMContentLoaded", handler); // Page chargée

// L'objet event
btn.addEventListener("click", (event) => {
  console.log(event.target);  // L'élément cliqué
  event.preventDefault();     // Empêcher l'action par défaut
});
```

---

## Exemple complet — Todo List

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <script src="https://cdn.tailwindcss.com"></script>
  <title>Todo List</title>
</head>
<body class="bg-gray-100 min-h-screen p-8">
  <div class="max-w-md mx-auto bg-white rounded-xl shadow p-6">
    <h1 class="text-2xl font-bold mb-4">Ma Todo List</h1>
    <div class="flex gap-2 mb-4">
      <input id="input" type="text" placeholder="Nouvelle tâche..."
        class="flex-1 border rounded px-3 py-2 focus:outline-none focus:border-blue-500">
      <button id="ajouter"
        class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 transition">
        Ajouter
      </button>
    </div>
    <ul id="liste" class="space-y-2"></ul>
  </div>

  <script>
    const input = document.getElementById("input");
    const btnAjouter = document.getElementById("ajouter");
    const liste = document.getElementById("liste");

    function ajouterTache() {
      const texte = input.value.trim();
      if (!texte) return;

      const li = document.createElement("li");
      li.className = "flex justify-between items-center p-3 bg-gray-50 rounded";
      li.innerHTML = `
        <span>${texte}</span>
        <button class="text-red-400 hover:text-red-600 font-bold" onclick="this.parentElement.remove()">✕</button>
      `;
      liste.appendChild(li);
      input.value = "";
      input.focus();
    }

    btnAjouter.addEventListener("click", ajouterTache);
    input.addEventListener("keydown", (e) => {
      if (e.key === "Enter") ajouterTache();
    });
  </script>
</body>
</html>
```

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| freeCodeCamp JS | [https://www.freecodecamp.org/learn/javascript-algorithms-and-data-structures/](https://www.freecodecamp.org/learn/javascript-algorithms-and-data-structures/) | Cours JS complet |
| JavaScript.info | [https://fr.javascript.info/](https://fr.javascript.info/) | Référence moderne en français |
| Exercism JS | [https://exercism.org/tracks/javascript](https://exercism.org/tracks/javascript) | Exercices avec feedback |
| Codecademy JS | [https://www.codecademy.com/learn/introduction-to-javascript](https://www.codecademy.com/learn/introduction-to-javascript) | Cours interactif |
| W3Schools JS | [https://www.w3schools.com/js/js_exercises.asp](https://www.w3schools.com/js/js_exercises.asp) | Exercices pratiques |

---

[← Tailwind](03-tailwind.md) | [Retour au README](../README.md) | [jQuery →](05-jquery.md)
