# Chapitre 02 — CSS

## Qu'est-ce que le CSS ?

Le **CSS** (Cascading Style Sheets) est le langage qui donne du **style** à une page HTML. Il contrôle les couleurs, les polices, les marges, la disposition des éléments, etc.

Sans CSS, une page HTML ressemble à un document texte brut. Avec CSS, elle devient une vraie interface visuelle.

---

## Comment lier CSS à HTML

### Méthode recommandée — Fichier externe
```html
<!-- Dans le <head> du HTML -->
<link rel="stylesheet" href="style.css">
```

### Dans le HTML (à éviter)
```html
<!-- Balise <style> dans le <head> -->
<style>
  h1 { color: red; }
</style>

<!-- Style inline (à éviter absolument) -->
<p style="color: red;">Texte</p>
```

---

## Syntaxe CSS

```css
/* sélecteur { propriété: valeur; } */

h1 {
  color: blue;
  font-size: 32px;
}
```

---

## Les sélecteurs

```css
/* Sélecteur de balise */
p { color: black; }

/* Sélecteur de classe (. devant le nom) */
.important { font-weight: bold; }

/* Sélecteur d'id (# devant le nom) */
#header { background-color: navy; }

/* Plusieurs sélecteurs */
h1, h2, h3 { font-family: Arial; }

/* Sélecteur descendant */
div p { color: gray; }

/* Sélecteur enfant direct */
ul > li { list-style: none; }

/* Pseudo-classe */
a:hover { color: red; }
button:active { opacity: 0.8; }
input:focus { border-color: blue; }
```

---

## Les propriétés essentielles

### Texte
```css
p {
  color: #333333;           /* Couleur du texte */
  font-size: 16px;          /* Taille de la police */
  font-family: Arial, sans-serif; /* Police */
  font-weight: bold;        /* Graisse (normal, bold, 100-900) */
  font-style: italic;       /* Style */
  text-align: center;       /* Alignement (left, right, center, justify) */
  text-decoration: underline; /* Décoration */
  line-height: 1.6;         /* Hauteur de ligne */
  letter-spacing: 1px;      /* Espacement lettres */
}
```

### Couleurs
```css
div {
  color: red;                /* Nom de couleur */
  color: #FF0000;            /* Hexadécimal */
  color: rgb(255, 0, 0);     /* RGB */
  color: rgba(255, 0, 0, 0.5); /* RGB avec transparence */
  background-color: #f0f0f0;
}
```

### Le modèle de boîte (Box Model)
```
┌──────────────────────────────┐
│           MARGIN             │
│  ┌────────────────────────┐  │
│  │        BORDER          │  │
│  │  ┌──────────────────┐  │  │
│  │  │     PADDING      │  │  │
│  │  │  ┌────────────┐  │  │  │
│  │  │  │  CONTENT   │  │  │  │
│  │  │  └────────────┘  │  │  │
│  │  └──────────────────┘  │  │
│  └────────────────────────┘  │
└──────────────────────────────┘
```

```css
div {
  width: 300px;
  height: 200px;
  padding: 20px;             /* Espace intérieur */
  margin: 10px;              /* Espace extérieur */
  border: 2px solid black;   /* Bordure */
  border-radius: 8px;        /* Coins arrondis */
  box-sizing: border-box;    /* Include padding dans la width */
}

/* Notation raccourcie (haut droite bas gauche) */
margin: 10px 20px 10px 20px;
/* ou haut/bas, gauche/droite */
padding: 10px 20px;
```

### Affichage et positionnement
```css
/* Display */
div { display: block; }    /* Prend toute la largeur */
span { display: inline; }  /* Suit le texte */
div { display: none; }     /* Cache l'élément */

/* Flexbox — disposition en ligne ou colonne */
.container {
  display: flex;
  flex-direction: row;        /* row | column */
  justify-content: center;    /* Alignement axe principal */
  align-items: center;        /* Alignement axe secondaire */
  gap: 16px;                  /* Espacement entre éléments */
  flex-wrap: wrap;            /* Retour à la ligne */
}

/* Position */
div { position: relative; }   /* Position normale + décalage possible */
div { position: absolute; }   /* Par rapport au parent relatif */
div { position: fixed; }      /* Fixe dans la fenêtre */
div { position: sticky; }     /* Collant lors du scroll */

div {
  position: absolute;
  top: 10px;
  left: 20px;
}
```

### Dimensions et overflow
```css
div {
  width: 100%;
  max-width: 1200px;
  min-width: 300px;
  height: 400px;
  overflow: hidden;    /* hidden | scroll | auto */
}
```

---

## Exemple complet

**HTML (index.html)**
```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <link rel="stylesheet" href="style.css">
  <title>Ma page stylée</title>
</head>
<body>
  <header class="header">
    <h1>Mon site</h1>
    <nav>
      <a href="#">Accueil</a>
      <a href="#">À propos</a>
      <a href="#">Contact</a>
    </nav>
  </header>
  <main class="container">
    <div class="card">
      <h2>Carte exemple</h2>
      <p>Voici une carte avec du style CSS.</p>
      <button class="btn">Cliquer</button>
    </div>
  </main>
</body>
</html>
```

**CSS (style.css)**
```css
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: Arial, sans-serif;
  background-color: #f5f5f5;
  color: #333;
}

.header {
  background-color: #2c3e50;
  color: white;
  padding: 20px 40px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.header nav a {
  color: white;
  text-decoration: none;
  margin-left: 20px;
}

.header nav a:hover {
  text-decoration: underline;
}

.container {
  max-width: 800px;
  margin: 40px auto;
  padding: 0 20px;
}

.card {
  background-color: white;
  border-radius: 8px;
  padding: 24px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.btn {
  background-color: #3498db;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 12px;
}

.btn:hover {
  background-color: #2980b9;
}
```

---

## CSS Grid (disposition en grille)

```css
.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr); /* 3 colonnes égales */
  gap: 20px;
}

/* Grille responsive */
.grid {
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
}
```

---

## Media Queries — Design responsive

```css
/* Mobile first */
.container {
  width: 100%;
  padding: 10px;
}

/* Tablette (768px et plus) */
@media (min-width: 768px) {
  .container {
    max-width: 720px;
    margin: 0 auto;
  }
}

/* Desktop (1024px et plus) */
@media (min-width: 1024px) {
  .container {
    max-width: 1200px;
  }
}
```

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| freeCodeCamp | [https://www.freecodecamp.org/learn/2022/responsive-web-design/](https://www.freecodecamp.org/learn/2022/responsive-web-design/) | Cours HTML/CSS complet |
| CSS Diner | [https://flukeout.github.io/](https://flukeout.github.io/) | Apprendre les sélecteurs CSS en jouant |
| Flexbox Froggy | [https://flexboxfroggy.com/#fr](https://flexboxfroggy.com/#fr) | Apprendre Flexbox en jouant |
| Grid Garden | [https://cssgridgarden.com/#fr](https://cssgridgarden.com/#fr) | Apprendre CSS Grid en jouant |
| W3Schools CSS | [https://www.w3schools.com/css/css_exercises.asp](https://www.w3schools.com/css/css_exercises.asp) | Exercices CSS |
| CSS Battle | [https://cssbattle.dev/](https://cssbattle.dev/) | Défis CSS |

---

[← HTML](01-html.md) | [Retour au README](../README.md) | [Tailwind CSS →](03-tailwind.md)
