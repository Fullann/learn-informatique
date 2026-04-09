# Chapitre 03 — Tailwind CSS

## Qu'est-ce que Tailwind CSS ?

**Tailwind CSS** est un framework CSS "utilitaire". Au lieu d'écrire du CSS dans un fichier séparé, tu appliques directement des **classes prédéfinies** dans ton HTML.

**CSS classique :**
```css
.bouton {
  background-color: blue;
  color: white;
  padding: 8px 16px;
  border-radius: 4px;
}
```
```html
<button class="bouton">Cliquer</button>
```

**Avec Tailwind :**
```html
<button class="bg-blue-500 text-white px-4 py-2 rounded">Cliquer</button>
```

---

## Installation

### Option 1 — CDN (pour tester rapidement)
```html
<!-- Dans le <head> -->
<script src="https://cdn.tailwindcss.com"></script>
```

### Option 2 — npm (pour les vrais projets)
```bash
npm install -D tailwindcss
npx tailwindcss init
```

Dans `tailwind.config.js` :
```js
module.exports = {
  content: ["./*.html"],
  theme: { extend: {} },
  plugins: [],
}
```

Dans `input.css` :
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Compiler :
```bash
npx tailwindcss -i ./input.css -o ./output.css --watch
```

---

## Les classes utilitaires essentielles

### Couleurs
```html
<!-- Texte -->
<p class="text-red-500">Rouge</p>
<p class="text-blue-700">Bleu foncé</p>
<p class="text-gray-400">Gris clair</p>

<!-- Fond -->
<div class="bg-green-200">Fond vert clair</div>
<div class="bg-slate-800">Fond gris foncé</div>
```

Les niveaux de couleur vont de **100** (très clair) à **900** (très foncé).
Couleurs disponibles : `red`, `orange`, `yellow`, `green`, `teal`, `blue`, `indigo`, `purple`, `pink`, `gray`, `slate`, `zinc`, `white`, `black`

### Espacement (margin & padding)
```html
<!-- Padding -->
<div class="p-4">Padding de 1rem partout</div>
<div class="px-4 py-2">px = gauche/droite, py = haut/bas</div>
<div class="pt-2 pb-4 pl-8 pr-0">Chaque côté séparément</div>

<!-- Margin -->
<div class="m-4">Margin partout</div>
<div class="mx-auto">Centrer horizontalement</div>
<div class="mt-8 mb-4">Haut et bas</div>
```

Échelle : `0`, `1` (4px), `2` (8px), `3` (12px), `4` (16px), `6` (24px), `8` (32px), `12` (48px), `16` (64px)…

### Typographie
```html
<p class="text-sm">Petit texte</p>
<p class="text-base">Texte normal</p>
<p class="text-lg">Grand texte</p>
<p class="text-xl">Très grand</p>
<p class="text-2xl">Encore plus grand</p>
<p class="text-4xl font-bold">Titre bold</p>

<p class="font-thin">Fin</p>
<p class="font-normal">Normal</p>
<p class="font-semibold">Semi-gras</p>
<p class="font-bold">Gras</p>
<p class="font-black">Très gras</p>

<p class="italic">Italique</p>
<p class="uppercase">MAJUSCULES</p>
<p class="text-center">Centré</p>
<p class="text-right">Droite</p>
<p class="leading-relaxed">Interligne relax</p>
```

### Dimensions
```html
<div class="w-full">Largeur 100%</div>
<div class="w-1/2">Largeur 50%</div>
<div class="w-64">Largeur 256px</div>
<div class="max-w-xl">Largeur max 576px</div>
<div class="max-w-screen-lg mx-auto">Centré avec max largeur</div>

<div class="h-16">Hauteur 64px</div>
<div class="h-screen">Hauteur plein écran</div>
<div class="min-h-screen">Min hauteur plein écran</div>
```

### Flexbox
```html
<div class="flex items-center justify-between gap-4">
  <div>Gauche</div>
  <div>Droite</div>
</div>

<div class="flex flex-col items-center">
  <div>Haut</div>
  <div>Bas</div>
</div>

<!-- justify : start | end | center | between | around | evenly -->
<!-- items : start | end | center | baseline | stretch -->
```

### Grid
```html
<div class="grid grid-cols-3 gap-4">
  <div>Col 1</div>
  <div>Col 2</div>
  <div>Col 3</div>
</div>

<!-- Responsive grid -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  <!-- ... -->
</div>
```

### Bordures et ombres
```html
<div class="border border-gray-300 rounded-lg shadow-md p-4">
  Carte avec bordure et ombre
</div>

<div class="border-2 border-blue-500 rounded-full">Cercle</div>

<!-- Ombres : shadow-sm | shadow | shadow-md | shadow-lg | shadow-xl -->
<!-- Coins : rounded-sm | rounded | rounded-md | rounded-lg | rounded-xl | rounded-full -->
```

### États (hover, focus, active)
```html
<button class="bg-blue-500 hover:bg-blue-700 text-white px-4 py-2 rounded transition">
  Hover moi
</button>

<input class="border focus:border-blue-500 focus:outline-none p-2 rounded">

<button class="active:scale-95 transition">Cliquer</button>
```

---

## Design Responsive

Les breakpoints Tailwind :

| Préfixe | Taille minimale |
|---------|----------------|
| `sm:` | 640px |
| `md:` | 768px |
| `lg:` | 1024px |
| `xl:` | 1280px |
| `2xl:` | 1536px |

```html
<!-- Mobile: 1 colonne, tablette: 2, desktop: 3 -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  <div class="p-4 bg-white shadow rounded">Carte 1</div>
  <div class="p-4 bg-white shadow rounded">Carte 2</div>
  <div class="p-4 bg-white shadow rounded">Carte 3</div>
</div>

<!-- Texte qui change de taille selon l'écran -->
<h1 class="text-2xl md:text-4xl lg:text-6xl font-bold">
  Titre responsive
</h1>
```

---

## Exemple complet — Page de présentation

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script src="https://cdn.tailwindcss.com"></script>
  <title>Ma page Tailwind</title>
</head>
<body class="bg-gray-50 text-gray-800">

  <!-- Navbar -->
  <nav class="bg-white shadow-sm sticky top-0">
    <div class="max-w-5xl mx-auto px-6 py-4 flex justify-between items-center">
      <span class="text-xl font-bold text-blue-600">MonSite</span>
      <div class="flex gap-6">
        <a href="#" class="hover:text-blue-600 transition">Accueil</a>
        <a href="#" class="hover:text-blue-600 transition">Projets</a>
        <a href="#" class="hover:text-blue-600 transition">Contact</a>
      </div>
    </div>
  </nav>

  <!-- Hero -->
  <section class="max-w-5xl mx-auto px-6 py-20 text-center">
    <h1 class="text-5xl font-black mb-4">Bonjour, je suis <span class="text-blue-600">Alice</span></h1>
    <p class="text-xl text-gray-500 mb-8">Développeuse web passionnée</p>
    <a href="#" class="bg-blue-600 hover:bg-blue-700 text-white px-8 py-3 rounded-full font-semibold transition">
      Voir mes projets
    </a>
  </section>

  <!-- Cartes -->
  <section class="max-w-5xl mx-auto px-6 py-10">
    <h2 class="text-3xl font-bold mb-8 text-center">Mes compétences</h2>
    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
      <div class="bg-white rounded-xl p-6 shadow hover:shadow-md transition">
        <div class="text-4xl mb-3">🌐</div>
        <h3 class="text-xl font-semibold mb-2">HTML/CSS</h3>
        <p class="text-gray-500">Structure et style des pages web.</p>
      </div>
      <div class="bg-white rounded-xl p-6 shadow hover:shadow-md transition">
        <div class="text-4xl mb-3">⚡</div>
        <h3 class="text-xl font-semibold mb-2">JavaScript</h3>
        <p class="text-gray-500">Interactivité et dynamisme.</p>
      </div>
      <div class="bg-white rounded-xl p-6 shadow hover:shadow-md transition">
        <div class="text-4xl mb-3">🐘</div>
        <h3 class="text-xl font-semibold mb-2">PHP</h3>
        <p class="text-gray-500">Développement backend.</p>
      </div>
    </div>
  </section>

</body>
</html>
```

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| Documentation officielle | [https://tailwindcss.com/docs](https://tailwindcss.com/docs) | Référence complète |
| Tailwind Play | [https://play.tailwindcss.com/](https://play.tailwindcss.com/) | Éditeur en ligne Tailwind |
| Tailwind Components | [https://tailwindcomponents.com/](https://tailwindcomponents.com/) | Composants réutilisables |
| Flowbite | [https://flowbite.com/](https://flowbite.com/) | Bibliothèque de composants Tailwind |

---

[← CSS](02-css.md) | [Retour au README](../README.md) | [JavaScript →](04-javascript.md)
