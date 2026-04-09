# Chapitre 06 — PHP

## Qu'est-ce que PHP ?

**PHP** (PHP: Hypertext Preprocessor) est un langage de programmation **côté serveur**. Contrairement à HTML/CSS/JS qui s'exécutent dans le navigateur, PHP s'exécute sur le **serveur web** avant d'envoyer la page au visiteur.

**Exemples d'usage :**
- Générer du HTML dynamique
- Interagir avec une base de données
- Gérer les formulaires et les sessions utilisateur
- Créer des APIs

PHP fait tourner WordPress, Wikipedia, Facebook (historiquement), et des millions de sites.

---

## Installation

### Option 1 — XAMPP (recommandé pour débutants)
Télécharger sur [https://www.apachefriends.org/fr/index.html](https://www.apachefriends.org/fr/index.html)
XAMPP installe Apache (serveur web) + PHP + MySQL.

Tes fichiers PHP vont dans `C:/xampp/htdocs/` (Windows) ou `/Applications/XAMPP/htdocs/` (Mac).
Puis ouvre `http://localhost/monfichier.php` dans ton navigateur.

### Option 2 — PHP intégré (PHP 5.4+)
```bash
php -S localhost:8000
# Ouvre http://localhost:8000
```

---

## Syntaxe de base

Tout code PHP s'écrit entre `<?php` et `?>`.

```php
<?php
echo "Bonjour le monde !";
?>
```

Un fichier PHP peut mélanger HTML et PHP :
```php
<!DOCTYPE html>
<html>
<body>
  <h1><?php echo "Bonjour !"; ?></h1>
  <p>Nous sommes le <?php echo date("d/m/Y"); ?></p>
</body>
</html>
```

---

## Variables et types

```php
<?php
// Les variables commencent par $
$prenom = "Alice";
$age = 25;
$taille = 1.68;
$estMajeur = true;
$rien = null;

// Afficher
echo $prenom;
echo "Bonjour " . $prenom;  // Concaténation avec .
echo "Bonjour $prenom";      // Interpolation dans les ""
echo "Âge : {$age} ans";    // Interpolation avec accolades

// var_dump — afficher le type ET la valeur (utile pour déboguer)
var_dump($age);       // int(25)
var_dump($estMajeur); // bool(true)

// print_r — afficher les tableaux
print_r([1, 2, 3]);
?>
```

---

## Opérateurs

```php
<?php
// Arithmétique
$a = 10 + 3;   // 13
$b = 10 - 3;   // 7
$c = 10 * 3;   // 30
$d = 10 / 3;   // 3.333...
$e = 10 % 3;   // 1
$f = 2 ** 3;   // 8

// Comparaison
5 == "5"    // true  (valeur seulement)
5 === "5"   // false (valeur + type)
5 != 6      // true
5 !== "5"   // true

// Logique
true && false  // false
true || false  // true
!true          // false
?>
```

---

## Structures de contrôle

### Conditions
```php
<?php
$age = 20;

if ($age >= 18) {
  echo "Majeur";
} elseif ($age >= 13) {
  echo "Adolescent";
} else {
  echo "Enfant";
}

// Ternaire
$statut = $age >= 18 ? "majeur" : "mineur";

// Switch
$jour = "lundi";
switch ($jour) {
  case "lundi":
    echo "Début de semaine";
    break;
  case "vendredi":
    echo "Bientôt le week-end !";
    break;
  default:
    echo "Jour ordinaire";
}
?>
```

### Boucles
```php
<?php
// for
for ($i = 0; $i < 5; $i++) {
  echo $i . " ";  // 0 1 2 3 4
}

// while
$i = 0;
while ($i < 3) {
  echo $i;
  $i++;
}

// foreach (pour les tableaux)
$fruits = ["pomme", "banane", "cerise"];
foreach ($fruits as $fruit) {
  echo $fruit . "<br>";
}

// foreach avec clé => valeur
$personne = ["prenom" => "Alice", "age" => 25];
foreach ($personne as $cle => $valeur) {
  echo "$cle : $valeur<br>";
}
?>
```

---

## Fonctions

```php
<?php
// Déclarer une fonction
function saluer($nom) {
  return "Bonjour $nom !";
}

echo saluer("Alice"); // "Bonjour Alice !"

// Paramètre par défaut
function salutation($nom = "invité") {
  return "Bonjour $nom";
}

echo salutation();        // "Bonjour invité"
echo salutation("Bob");   // "Bonjour Bob"

// Type hints (PHP 7+)
function additionner(int $a, int $b): int {
  return $a + $b;
}

// Fonctions anonymes (closures)
$doubler = function($n) {
  return $n * 2;
};
echo $doubler(5); // 10
?>
```

---

## Tableaux

```php
<?php
// Tableau indexé
$fruits = ["pomme", "banane", "cerise"];
echo $fruits[0];        // "pomme"
echo count($fruits);    // 3

// Ajouter
$fruits[] = "kiwi";     // À la fin
array_push($fruits, "mangue");

// Tableau associatif (clé => valeur)
$personne = [
  "prenom" => "Alice",
  "age" => 25,
  "ville" => "Paris"
];
echo $personne["prenom"]; // "Alice"

// Fonctions utiles
sort($fruits);                  // Trier
array_reverse($fruits);        // Inverser
in_array("pomme", $fruits);    // Vérifier présence (true/false)
array_search("pomme", $fruits); // Trouver l'index
array_merge($fruits, ["kiwi"]); // Fusionner
array_slice($fruits, 1, 2);    // Extraire une portion
implode(", ", $fruits);         // Tableau → chaîne
explode(",", "a,b,c");          // Chaîne → tableau
?>
```

---

## Chaînes de caractères

```php
<?php
$texte = "Bonjour le monde";

strlen($texte);               // 17
strtoupper($texte);           // "BONJOUR LE MONDE"
strtolower($texte);           // "bonjour le monde"
trim("  hello  ");            // "hello"
str_replace("monde", "PHP", $texte); // "Bonjour le PHP"
strpos($texte, "monde");      // 11 (position)
substr($texte, 0, 7);         // "Bonjour"
str_contains($texte, "monde"); // true (PHP 8+)
str_repeat("*", 5);           // "*****"
number_format(1234567.89, 2, ",", " "); // "1 234 567,89"
?>
```

---

## Formulaires HTML + PHP

**formulaire.html**
```html
<form action="traitement.php" method="post">
  <input type="text" name="prenom" placeholder="Ton prénom">
  <input type="email" name="email" placeholder="Ton email">
  <button type="submit">Envoyer</button>
</form>
```

**traitement.php**
```php
<?php
// Récupérer les données POST
$prenom = $_POST["prenom"] ?? "";
$email = $_POST["email"] ?? "";

// Valider
if (empty($prenom)) {
  echo "Le prénom est requis.";
  exit;
}

if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
  echo "Email invalide.";
  exit;
}

// Toujours sécuriser avant d'afficher !
$prenomSafe = htmlspecialchars($prenom);
echo "Bonjour $prenomSafe, ton email est $email !";
?>
```

> **Sécurité** : Toujours utiliser `htmlspecialchars()` avant d'afficher une donnée utilisateur pour éviter les attaques XSS.

---

## Sessions et cookies

```php
<?php
// Démarrer une session
session_start();

// Stocker dans la session
$_SESSION["utilisateur"] = "Alice";
$_SESSION["role"] = "admin";

// Lire la session
echo $_SESSION["utilisateur"]; // "Alice"

// Détruire la session (déconnexion)
session_destroy();

// Cookies
setcookie("theme", "dark", time() + 3600 * 24 * 30); // 30 jours
echo $_COOKIE["theme"]; // "dark"
?>
```

---

## Connexion à une base de données (MySQL)

```php
<?php
// Connexion avec PDO (recommandé)
$pdo = new PDO(
  "mysql:host=localhost;dbname=mabase;charset=utf8",
  "root",
  "",
  [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION]
);

// Requête SELECT
$stmt = $pdo->query("SELECT * FROM utilisateurs");
$utilisateurs = $stmt->fetchAll(PDO::FETCH_ASSOC);
foreach ($utilisateurs as $user) {
  echo $user["prenom"] . "<br>";
}

// Requête préparée (pour éviter les injections SQL)
$stmt = $pdo->prepare("SELECT * FROM utilisateurs WHERE id = ?");
$stmt->execute([42]);
$user = $stmt->fetch(PDO::FETCH_ASSOC);
echo $user["prenom"];

// INSERT
$stmt = $pdo->prepare("INSERT INTO utilisateurs (prenom, email) VALUES (?, ?)");
$stmt->execute(["Alice", "alice@email.com"]);
?>
```

---

## Exemple complet — Application To-Do avec PHP

**index.php**
```php
<?php
// Simulation d'une base de données en session
session_start();
if (!isset($_SESSION["todos"])) {
  $_SESSION["todos"] = [];
}

// Ajouter une tâche
if ($_SERVER["REQUEST_METHOD"] === "POST" && !empty($_POST["tache"])) {
  $_SESSION["todos"][] = htmlspecialchars($_POST["tache"]);
}

// Supprimer une tâche
if (isset($_GET["supprimer"])) {
  $index = (int) $_GET["supprimer"];
  array_splice($_SESSION["todos"], $index, 1);
  header("Location: index.php");
  exit;
}
?>
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <script src="https://cdn.tailwindcss.com"></script>
  <title>Todo PHP</title>
</head>
<body class="bg-gray-100 p-8">
  <div class="max-w-md mx-auto bg-white rounded-xl shadow p-6">
    <h1 class="text-2xl font-bold mb-4">Todo List PHP</h1>
    <form method="post" class="flex gap-2 mb-4">
      <input type="text" name="tache" placeholder="Nouvelle tâche..."
        class="flex-1 border rounded px-3 py-2">
      <button class="bg-blue-500 text-white px-4 py-2 rounded">Ajouter</button>
    </form>
    <ul class="space-y-2">
      <?php foreach ($_SESSION["todos"] as $index => $tache): ?>
        <li class="flex justify-between items-center p-3 bg-gray-50 rounded">
          <span><?= $tache ?></span>
          <a href="?supprimer=<?= $index ?>" class="text-red-400 hover:text-red-600 font-bold">✕</a>
        </li>
      <?php endforeach; ?>
      <?php if (empty($_SESSION["todos"])): ?>
        <li class="text-gray-400 text-center py-4">Aucune tâche</li>
      <?php endif; ?>
    </ul>
  </div>
</body>
</html>
```

---

## Sites pour pratiquer

| Site | Lien | Description |
|------|------|-------------|
| PHP.net | [https://www.php.net/manual/fr/](https://www.php.net/manual/fr/) | Documentation officielle en français |
| W3Schools PHP | [https://www.w3schools.com/php/php_exercises.asp](https://www.w3schools.com/php/php_exercises.asp) | Exercices pratiques |
| Exercism PHP | [https://exercism.org/tracks/php](https://exercism.org/tracks/php) | Exercices avec feedback |
| OpenClassrooms PHP | [https://openclassrooms.com/fr/courses/918836-concevez-votre-site-web-avec-php-et-mysql](https://openclassrooms.com/fr/courses/918836-concevez-votre-site-web-avec-php-et-mysql) | Cours PHP/MySQL en français |

---

[← jQuery](05-jquery.md) | [Retour au README](../README.md) | [Parcours C++ →](../cpp/01-introduction.md)
