# Chapitre 01 — HTML

## Qu'est-ce que le HTML ?

Le **HTML** (HyperText Markup Language) est le langage de base du web. Il décrit la **structure** d'une page web grâce à des **balises** (tags).

Un fichier HTML n'est pas un programme : c'est un document que le navigateur interprète pour afficher une page.

---

## Structure de base

```html
<!DOCTYPE html>
<html lang="fr">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ma première page</title>
  </head>
  <body>
    <h1>Bonjour le monde !</h1>
    <p>C'est ma première page HTML.</p>
  </body>
</html>
```

| Balise | Rôle |
|--------|------|
| `<!DOCTYPE html>` | Indique au navigateur que c'est du HTML5 |
| `<html>` | Racine du document |
| `<head>` | Métadonnées (non visibles) |
| `<body>` | Contenu visible de la page |

---

## Les balises essentielles

### Titres
```html
<h1>Titre principal</h1>
<h2>Sous-titre</h2>
<h3>Sous-sous-titre</h3>
<!-- de h1 à h6 -->
```

### Paragraphes et texte
```html
<p>Un paragraphe de texte.</p>
<strong>Texte en gras</strong>
<em>Texte en italique</em>
<br> <!-- Saut de ligne -->
<hr> <!-- Ligne horizontale -->
```

### Liens
```html
<a href="https://example.com">Clique ici</a>
<a href="page2.html">Page interne</a>
<a href="https://example.com" target="_blank">Ouvrir dans un nouvel onglet</a>
```

### Images
```html
<img src="photo.jpg" alt="Description de l'image">
<img src="https://picsum.photos/200" alt="Image aléatoire" width="200">
```

### Listes
```html
<!-- Liste non ordonnée -->
<ul>
  <li>Pomme</li>
  <li>Banane</li>
  <li>Cerise</li>
</ul>

<!-- Liste ordonnée -->
<ol>
  <li>Premier</li>
  <li>Deuxième</li>
  <li>Troisième</li>
</ol>
```

### Tableaux
```html
<table>
  <thead>
    <tr>
      <th>Nom</th>
      <th>Âge</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Alice</td>
      <td>25</td>
    </tr>
    <tr>
      <td>Bob</td>
      <td>30</td>
    </tr>
  </tbody>
</table>
```

### Formulaires
```html
<form action="/envoyer" method="post">
  <label for="nom">Nom :</label>
  <input type="text" id="nom" name="nom" placeholder="Ton prénom">

  <label for="email">Email :</label>
  <input type="email" id="email" name="email">

  <label for="message">Message :</label>
  <textarea id="message" name="message" rows="4"></textarea>

  <button type="submit">Envoyer</button>
</form>
```

### Conteneurs sémantiques
```html
<header>En-tête du site</header>
<nav>Menu de navigation</nav>
<main>
  <article>Un article</article>
  <section>Une section</section>
  <aside>Contenu secondaire</aside>
</main>
<footer>Pied de page</footer>

<!-- Conteneur générique -->
<div>Bloc générique</div>
<span>Inline générique</span>
```

---

## Attributs importants

```html
<!-- id : identifiant unique -->
<p id="intro">Premier paragraphe</p>

<!-- class : groupe d'éléments (utilisé avec CSS) -->
<p class="important">Paragraphe important</p>

<!-- style : CSS inline (à éviter, préférer un fichier CSS) -->
<p style="color: red;">Texte rouge</p>
```

---

## Exercice pratique — Crée ta première page

Crée un fichier `index.html` avec :
- Un titre principal avec ton prénom
- Un paragraphe de présentation
- Une liste de tes 3 films préférés
- Un lien vers ton site préféré
- Une image (tu peux utiliser `https://picsum.photos/300`)

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| freeCodeCamp | [https://www.freecodecamp.org/learn/2022/responsive-web-design/](https://www.freecodecamp.org/learn/2022/responsive-web-design/) | Cours interactif HTML gratuit |
| W3Schools Try it | [https://www.w3schools.com/html/html_exercises.asp](https://www.w3schools.com/html/html_exercises.asp) | Exercices HTML avec éditeur en ligne |
| The Odin Project | [https://www.theodinproject.com/paths/foundations/courses/foundations](https://www.theodinproject.com/paths/foundations/courses/foundations) | Parcours complet web |
| Codecademy | [https://www.codecademy.com/learn/learn-html](https://www.codecademy.com/learn/learn-html) | Cours interactif HTML |

---

[← Retour au README](../README.md) | [Chapitre suivant : CSS →](02-css.md)
