# Chapitre 05 — jQuery

## Qu'est-ce que jQuery ?

**jQuery** est une bibliothèque JavaScript créée en 2006. Elle simplifie énormément la manipulation du DOM, la gestion des événements, les animations et les requêtes AJAX.

Sa devise : **"Write less, do more."**

Même si les frameworks modernes (React, Vue) ont réduit son usage, jQuery reste présent dans de nombreux projets existants et est important à connaître.

**Sans jQuery (JS vanilla) :**
```js
document.querySelectorAll(".btn").forEach(btn => {
  btn.addEventListener("click", function() {
    this.style.display = "none";
  });
});
```

**Avec jQuery :**
```js
$(".btn").click(function() {
  $(this).hide();
});
```

---

## Installation

### CDN (le plus simple)
```html
<!-- Dans le <head> ou avant </body> -->
<script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
```

### npm
```bash
npm install jquery
```

---

## Le sélecteur $()

jQuery utilise `$()` pour sélectionner des éléments, comme les sélecteurs CSS.

```js
$("p")            // Tous les <p>
$(".classe")      // Tous les éléments avec class="classe"
$("#monId")       // L'élément avec id="monId"
$("ul li")        // Tous les <li> dans un <ul>
$("input[type='text']")  // Inputs de type texte
$(this)           // L'élément courant dans un callback
```

---

## Attendre que la page soit chargée

```js
// Méthode classique
$(document).ready(function() {
  // Ton code ici
});

// Raccourci moderne (recommandé)
$(function() {
  // Ton code ici
});
```

---

## Manipuler le contenu

```js
// Lire / modifier le texte
$("#titre").text();             // Lire
$("#titre").text("Nouveau texte"); // Modifier

// Lire / modifier le HTML
$(".zone").html();              // Lire
$(".zone").html("<b>Gras</b>"); // Modifier

// Lire / modifier la valeur d'un input
$("input").val();               // Lire
$("input").val("valeur");       // Modifier
```

---

## Manipuler les attributs et classes

```js
// Attributs
$("img").attr("src");              // Lire
$("img").attr("src", "photo.jpg"); // Modifier
$("a").attr("href", "https://example.com");

// Classes
$("div").addClass("active");
$("div").removeClass("hidden");
$("div").toggleClass("visible");
$("div").hasClass("active");  // true / false
```

---

## Manipuler le style

```js
// css() — lire ou modifier du CSS
$("p").css("color");              // Lire
$("p").css("color", "red");       // Modifier

// Plusieurs propriétés à la fois
$("div").css({
  "background-color": "blue",
  "font-size": "18px",
  "padding": "10px"
});
```

---

## Afficher / cacher des éléments

```js
$(".box").hide();           // Cacher instantanément
$(".box").show();           // Afficher instantanément
$(".box").toggle();         // Basculer visible/caché

// Avec animation (durée en ms ou "slow"/"fast")
$(".box").hide(500);
$(".box").show("fast");
$(".box").toggle("slow");

// Fondu
$(".box").fadeIn(400);
$(".box").fadeOut(400);
$(".box").fadeToggle();

// Glissement
$(".box").slideDown(300);
$(".box").slideUp(300);
$(".box").slideToggle();
```

---

## Événements

```js
// Clic
$("button").click(function() {
  alert("Cliqué !");
});

// Double clic
$("div").dblclick(function() {
  $(this).css("background", "yellow");
});

// Survol
$("a").hover(
  function() { $(this).css("color", "red"); },    // mouseenter
  function() { $(this).css("color", "black"); }   // mouseleave
);

// Touche clavier
$("input").keyup(function(e) {
  console.log("Touche :", e.key);
});

// Soumission de formulaire
$("form").submit(function(e) {
  e.preventDefault(); // Empêcher le rechargement
  let valeur = $("input[name='nom']").val();
  console.log("Nom :", valeur);
});

// Méthode universelle on()
$(document).on("click", ".btn-dynamique", function() {
  // Fonctionne même pour les éléments ajoutés après coup
});
```

---

## Traverser le DOM

```js
$("li").parent()          // Élément parent
$("ul").children()        // Enfants directs
$("ul").children("li")    // Enfants directs filtrés
$("p").siblings()         // Frères et sœurs
$("li").first()           // Premier élément
$("li").last()            // Dernier élément
$("li").eq(2)             // 3ème élément (index 0)
$("li").next()            // Suivant
$("li").prev()            // Précédent
$(".box").find("p")       // Chercher dans les descendants
$("p").closest("div")     // Ancêtre le plus proche
```

---

## Créer et insérer des éléments

```js
// Créer un élément
let $newLi = $("<li>").text("Nouvel élément").addClass("item");

// Insérer
$("ul").append($newLi);          // À la fin
$("ul").prepend($newLi);         // Au début
$(".reference").after($newLi);   // Après
$(".reference").before($newLi);  // Avant

// Supprimer
$(".item").remove();             // Supprimer l'élément
$(".item").empty();              // Vider le contenu
```

---

## Itérer avec each()

```js
$("li").each(function(index, element) {
  console.log(index, $(element).text());
});

// Version raccourcie
$("li").each(function() {
  $(this).text($(this).text().toUpperCase());
});
```

---

## AJAX avec jQuery

```js
// GET — récupérer des données
$.get("https://api.example.com/users", function(data) {
  console.log(data);
});

// POST — envoyer des données
$.post("https://api.example.com/users", { nom: "Alice", age: 25 }, function(response) {
  console.log(response);
});

// $.ajax() — méthode complète
$.ajax({
  url: "https://jsonplaceholder.typicode.com/posts/1",
  method: "GET",
  success: function(data) {
    console.log(data.title);
  },
  error: function(err) {
    console.error("Erreur :", err);
  }
});

// Avec les Promises ($.ajax retourne un objet Deferred)
$.ajax({ url: "https://jsonplaceholder.typicode.com/posts" })
  .done(data => console.log(data))
  .fail(err => console.error(err));
```

---

## Exemple complet — Galerie filtrable

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
  <script src="https://cdn.tailwindcss.com"></script>
  <title>Galerie jQuery</title>
</head>
<body class="bg-gray-100 p-8">
  <div class="max-w-3xl mx-auto">
    <h1 class="text-3xl font-bold mb-6 text-center">Ma galerie</h1>

    <!-- Filtres -->
    <div class="flex gap-3 justify-center mb-6">
      <button class="filtre bg-blue-500 text-white px-4 py-2 rounded" data-filtre="all">Tous</button>
      <button class="filtre bg-white px-4 py-2 rounded border" data-filtre="nature">Nature</button>
      <button class="filtre bg-white px-4 py-2 rounded border" data-filtre="ville">Ville</button>
      <button class="filtre bg-white px-4 py-2 rounded border" data-filtre="portrait">Portrait</button>
    </div>

    <!-- Galerie -->
    <div id="galerie" class="grid grid-cols-3 gap-4">
      <div class="item nature rounded overflow-hidden shadow"><img src="https://picsum.photos/seed/1/300/200" class="w-full"></div>
      <div class="item ville rounded overflow-hidden shadow"><img src="https://picsum.photos/seed/2/300/200" class="w-full"></div>
      <div class="item portrait rounded overflow-hidden shadow"><img src="https://picsum.photos/seed/3/300/200" class="w-full"></div>
      <div class="item nature rounded overflow-hidden shadow"><img src="https://picsum.photos/seed/4/300/200" class="w-full"></div>
      <div class="item portrait rounded overflow-hidden shadow"><img src="https://picsum.photos/seed/5/300/200" class="w-full"></div>
      <div class="item ville rounded overflow-hidden shadow"><img src="https://picsum.photos/seed/6/300/200" class="w-full"></div>
    </div>
  </div>

  <script>
    $(function() {
      $(".filtre").click(function() {
        const filtre = $(this).data("filtre");

        // Mettre à jour les boutons
        $(".filtre").removeClass("bg-blue-500 text-white").addClass("bg-white");
        $(this).removeClass("bg-white").addClass("bg-blue-500 text-white");

        // Filtrer les éléments
        if (filtre === "all") {
          $(".item").fadeIn(300);
        } else {
          $(".item").hide();
          $(".item." + filtre).fadeIn(300);
        }
      });
    });
  </script>
</body>
</html>
```

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Documentation jQuery | [https://api.jquery.com/](https://api.jquery.com/) | Référence officielle |
| W3Schools jQuery | [https://www.w3schools.com/jquery/jquery_exercises.asp](https://www.w3schools.com/jquery/jquery_exercises.asp) | Exercices pratiques |
| Learn jQuery | [https://learn.jquery.com/](https://learn.jquery.com/) | Guide officiel d'apprentissage |

---

[← JavaScript](04-javascript.md) | [Retour au README](../README.md) | [PHP →](06-php.md)
