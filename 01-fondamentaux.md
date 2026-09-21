# 📘 Cours complet de JavaScript — Partie 1 : Les Fondamentaux

> Sources de référence utilisées : [MDN Web Docs – JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide), [W3Schools JS](https://www.w3schools.com/js), [javascript.info](https://javascript.info), ECMAScript (ECMA-262).

## Sommaire de cette partie
1. Introduction à JavaScript
2. Mise en place de l'environnement
3. Variables : `var`, `let`, `const`
4. Types de données
5. Opérateurs
6. Structures de contrôle
7. Fonctions
8. Mini-projets de la partie 1

---

## 1. Introduction à JavaScript

### 1.1 Qu'est-ce que JavaScript ?

JavaScript (JS) est un langage de programmation **interprété**, **orienté objet à base de prototypes**, **dynamique** et **multi-paradigme** (impératif, fonctionnel, orienté objet). Créé en 1995 par Brendan Eich pour Netscape, il est aujourd'hui le langage standard du Web, exécuté nativement dans tous les navigateurs, mais aussi côté serveur grâce à **Node.js**, dans les applications mobiles (React Native), le bureau (Electron), l'IoT, etc.

Le langage suit la spécification **ECMAScript (ES)**, maintenue par TC39. Chaque année sort une nouvelle version : ES6/ES2015 a été une révolution (classes, `let/const`, arrow functions, promesses...), suivie par des mises à jour annuelles (ES2016 → ES2025+).

### 1.2 JavaScript n'est pas Java

Malgré le nom, JavaScript et Java n'ont presque rien en commun (syntaxe différente, sémantique différente, usages différents). Le nom vient d'une stratégie marketing de l'époque.

### 1.3 Pourquoi apprendre JavaScript ?

- **Seul langage natif des navigateurs** pour rendre une page interactive (avec HTML/CSS).
- **Full-stack** : front (React, Vue, Angular, Svelte) et back (Node.js, Deno, Bun).
- **Écosystème immense** : npm, le plus grand registre de paquets au monde.
- **Marché de l'emploi** très large.

### 1.4 Comment le code JavaScript s'exécute-t-il ?

Un moteur JavaScript (V8 dans Chrome/Node.js, SpiderMonkey dans Firefox, JavaScriptCore dans Safari) :
1. **Parse** le code (analyse syntaxique → AST).
2. **Compile** en code machine (JIT — Just In Time compilation).
3. **Exécute** dans un environnement (navigateur ou Node.js) qui fournit des APIs supplémentaires (DOM, `fetch`, `fs`, etc., qui ne font PAS partie du langage JS lui-même).

---

## 2. Mise en place de l'environnement

### 2.1 Dans le navigateur

La façon la plus rapide de tester : ouvrez les **DevTools** (F12) → onglet **Console**.

### 2.2 Lier un script à une page HTML

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Mon premier script</title>
</head>
<body>
  <h1>Bonjour</h1>

  <!-- Bonne pratique : script juste avant </body>, ou avec defer -->
  <script src="script.js" defer></script>
</body>
</html>
```

- `defer` : le script est téléchargé en parallèle du parsing HTML, mais exécuté seulement après que le DOM soit prêt (ordre respecté). **Recommandé**.
- `async` : téléchargé en parallèle, exécuté dès que prêt (ordre NON garanti). Utile pour scripts indépendants (analytics).
- Script en fin de `<body>` sans attribut : solution historique, fonctionne aussi.

### 2.3 Node.js (exécution côté serveur / terminal)

Installez [Node.js](https://nodejs.org), puis :

```bash
node monfichier.js
```

Node permet aussi d'utiliser `npm` (Node Package Manager) pour installer des librairies : `npm init -y`, `npm install nom-du-paquet`.

### 2.4 Les commentaires

```javascript
// Commentaire sur une ligne

/* Commentaire
   sur plusieurs lignes */

/**
 * Commentaire JSDoc (documentation de fonction)
 * @param {number} a
 * @returns {number}
 */
function carre(a) { return a * a; }
```

### 🧪 Mini-projet 1 : « Hello World interactif »
Créez `index.html` + `script.js`. Dans `script.js` :
```javascript
console.log("Bonjour depuis JavaScript !");
alert("Bienvenue sur mon premier script JS");
document.body.style.backgroundColor = "#222";
document.body.style.color = "white";
```
**Objectif** : vérifier que le lien HTML ↔ JS fonctionne, utiliser `console.log`, `alert`, et manipuler un style basique.

---

## 3. Variables : `var`, `let`, `const`

### 3.1 Déclaration et affectation

```javascript
let age = 25;        // variable modifiable
const pi = 3.14159;  // constante, ne peut pas être réaffectée
var ancien = "à éviter"; // ancienne syntaxe (pré-ES6)
```

### 3.2 Règles de nommage

- Sensible à la casse : `age` ≠ `Age`.
- Doit commencer par une lettre, `_` ou `$`.
- Convention : **camelCase** (`nomUtilisateur`), constantes globales parfois en `SCREAMING_SNAKE_CASE` (`MAX_SIZE`).
- Mots réservés interdits (`function`, `class`, `return`...).

### 3.3 `var` vs `let` vs `const` — LA différence essentielle

| Caractéristique | `var` | `let` | `const` |
|---|---|---|---|
| Portée (scope) | Fonction | Bloc `{}` | Bloc `{}` |
| Redéclaration | Oui | Non | Non |
| Réaffectation | Oui | Oui | Non |
| Hoisting | Oui (initialisée à `undefined`) | Oui (mais "Temporal Dead Zone") | Oui (TDZ) |
| Attaché à `window` (navigateur) | Oui (scope global) | Non | Non |

**Portée de bloc — l'exemple classique :**
```javascript
if (true) {
  var x = 1;
  let y = 2;
}
console.log(x); // 1 (var "fuit" hors du bloc)
console.log(y); // ReferenceError : y n'existe pas ici
```

**Piège classique avec `var` dans une boucle :**
```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // affiche 3, 3, 3
}
for (let j = 0; j < 3; j++) {
  setTimeout(() => console.log(j), 100); // affiche 0, 1, 2
}
```
👉 `let` crée une **nouvelle liaison** à chaque itération, pas `var`. C'est une des raisons majeures de préférer `let`.

**`const` n'empêche pas la mutation d'un objet/tableau :**
```javascript
const utilisateur = { nom: "Awa" };
utilisateur.nom = "Fatou"; // ✅ autorisé (on modifie le contenu, pas la référence)
utilisateur = {};          // ❌ TypeError (on tente de changer la référence)

const tableau = [1, 2, 3];
tableau.push(4); // ✅ autorisé
```

### 3.4 Hoisting (élévation)

JavaScript "remonte" les déclarations en haut de leur portée avant exécution.

```javascript
console.log(a); // undefined (pas d'erreur, var est hoistée)
var a = 5;

console.log(b); // ReferenceError (Temporal Dead Zone)
let b = 5;
```

### ✅ Bonne pratique
Utilisez **`const` par défaut**, et **`let`** seulement si la variable doit changer. N'utilisez (quasiment) plus jamais `var`.

### 🧪 Mini-projet 2 : « Convertisseur de température »
```javascript
const celsiusVersFahrenheit = (c) => (c * 9) / 5 + 32;
let temperatureCelsius = 25;
let temperatureFahrenheit = celsiusVersFahrenheit(temperatureCelsius);
console.log(`${temperatureCelsius}°C = ${temperatureFahrenheit}°F`);
```
**Objectif** : pratiquer `const`/`let`, template literals (vues plus loin), et une fonction fléchée simple.

---

## 4. Types de données

JavaScript est un langage à **typage dynamique** (le type est associé à la valeur, pas à la variable) et **faiblement typé** (conversions implicites possibles).

### 4.1 Types primitifs (7 au total)

```javascript
let n1 = 42;                  // number
let n2 = 3.14;                // number (pas de distinction int/float)
let big = 123456789012345678n; // bigint (nombres entiers arbitrairement grands)
let s = "Bonjour";             // string
let b = true;                  // boolean
let u;                          // undefined (valeur non affectée)
let nul = null;                 // null (absence volontaire de valeur)
let sym = Symbol("id");         // symbol (valeur unique, souvent pour clés d'objets)
```

Vérifier le type avec `typeof` :
```javascript
typeof 42;          // "number"
typeof "texte";      // "string"
typeof true;          // "boolean"
typeof undefined;      // "undefined"
typeof null;            // "object"  ⚠️ bug historique du langage, connu et conservé
typeof Symbol();          // "symbol"
typeof 10n;                // "bigint"
typeof function(){};        // "function"
typeof {};                   // "object"
typeof [];                    // "object" (les tableaux sont des objets)
```

### 4.2 Le type `object` (type de référence)

Tout ce qui n'est pas primitif est un objet : `{}`, `[]`, `function`, `Date`, `Map`, `Set`, etc. Les objets sont **passés par référence**, contrairement aux primitifs qui sont **passés par valeur**.

```javascript
let a = 10;
let b = a;
b = 20;
console.log(a); // 10 (copie indépendante)

let obj1 = { valeur: 10 };
let obj2 = obj1;
obj2.valeur = 20;
console.log(obj1.valeur); // 20 (même référence !)
```

### 4.3 Conversion de types (coercition)

```javascript
// Conversion explicite
Number("42");     // 42
String(42);        // "42"
Boolean(1);          // true
Boolean(0);           // false
Boolean("");           // false
Boolean("texte");        // true
parseInt("42px");         // 42
parseFloat("3.14m");       // 3.14

// Conversion implicite (coercition) — attention aux pièges !
"5" + 3;     // "53"  (concaténation, le nombre devient string)
"5" - 3;      // 2     (soustraction, la string devient number)
"5" * "2";     // 10
true + true;    // 2
null + 1;        // 1
undefined + 1;    // NaN
```

### 4.4 Valeurs "falsy" et "truthy"

**Falsy** (évaluées à `false` dans un contexte booléen) : `false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, `NaN`.
Tout le reste est **truthy** (y compris `"0"`, `[]`, `{}` !).

```javascript
if ([]) console.log("un tableau vide est truthy !"); // s'affiche
if ("0") console.log("la string '0' est truthy !");   // s'affiche
```

### 4.5 `==` vs `===`

```javascript
5 == "5";   // true  (conversion de type avant comparaison)
5 === "5";  // false (compare aussi le type, PAS de conversion)
null == undefined;  // true
null === undefined; // false
NaN === NaN;         // false (!) — utiliser Number.isNaN(x) pour tester
```
### ✅ Bonne pratique : toujours utiliser `===` et `!==` (égalité stricte), sauf cas très spécifique.

### 🧪 Mini-projet 3 : « Testeur de types »
```javascript
function analyser(valeur) {
  console.log(`Valeur : ${JSON.stringify(valeur)}`);
  console.log(`Type : ${typeof valeur}`);
  console.log(`Truthy ? ${Boolean(valeur)}`);
}
[42, "texte", "", 0, null, undefined, [], {}, NaN, true].forEach(analyser);
```

---

## 5. Opérateurs

### 5.1 Arithmétiques
```javascript
10 + 3;  // 13
10 - 3;  // 7
10 * 3;  // 30
10 / 3;  // 3.333...
10 % 3;  // 1 (modulo, reste de la division)
10 ** 3; // 1000 (exposant)
++x; x++; --x; x--; // incrémentation / décrémentation
```

### 5.2 Affectation
```javascript
let x = 5;
x += 3;  // x = x + 3
x -= 2;  // x = x - 2
x *= 2;  // x = x * 2
x /= 2;  // x = x / 2
x **= 2; // x = x ** 2
x ??= 10; // affecte 10 seulement si x est null/undefined
x ||= 10; // affecte 10 seulement si x est falsy
x &&= 10; // affecte 10 seulement si x est truthy
```

### 5.3 Comparaison
```javascript
> < >= <= == != === !==
```

### 5.4 Logiques
```javascript
true && false; // false (ET)
true || false;  // true  (OU)
!true;           // false (NON)

// Court-circuit très utilisé en pratique :
const nom = utilisateur && utilisateur.nom; // évite une erreur si utilisateur est null
const valeurParDefaut = entree || "valeur par défaut";
```

### 5.5 Opérateur de coalescence des nuls `??`
```javascript
let valeur = 0;
console.log(valeur || "défaut");  // "défaut" (0 est falsy !)
console.log(valeur ?? "défaut");  //  0        (?? ne regarde que null/undefined)
```

### 5.6 Chaînage optionnel `?.`
```javascript
const utilisateur = { adresse: { ville: "Yaoundé" } };
console.log(utilisateur.adresse?.ville);       // "Yaoundé"
console.log(utilisateur.contact?.telephone);   // undefined (pas d'erreur !)
console.log(utilisateur.direCoucou?.());        // undefined si la méthode n'existe pas
```

### 5.7 Opérateur ternaire
```javascript
const age = 20;
const message = age >= 18 ? "Majeur" : "Mineur";
```

### 5.8 Opérateur `typeof`, `instanceof`, `in`
```javascript
typeof "x";                 // "string"
[] instanceof Array;         // true
"nom" in { nom: "Awa" };      // true
```

### 🧪 Mini-projet 4 : « Calculatrice en console »
```javascript
function calculer(a, operateur, b) {
  switch (operateur) {
    case "+": return a + b;
    case "-": return a - b;
    case "*": return a * b;
    case "/": return b !== 0 ? a / b : "Erreur : division par zéro";
    default: return "Opérateur inconnu";
  }
}
console.log(calculer(10, "+", 5));  // 15
console.log(calculer(10, "/", 0));  // Erreur : division par zéro
```

---

## 6. Structures de contrôle

### 6.1 `if / else if / else`
```javascript
const note = 14;
if (note >= 16) {
  console.log("Très bien");
} else if (note >= 12) {
  console.log("Bien");
} else if (note >= 10) {
  console.log("Passable");
} else {
  console.log("Insuffisant");
}
```

### 6.2 `switch`
```javascript
const jour = "mardi";
switch (jour) {
  case "lundi":
  case "mardi":
  case "mercredi":
  case "jeudi":
  case "vendredi":
    console.log("Jour de semaine");
    break;
  case "samedi":
  case "dimanche":
    console.log("Week-end");
    break;
  default:
    console.log("Jour inconnu");
}
```
⚠️ Ne pas oublier `break`, sinon exécution en cascade ("fall-through") — parfois voulu, souvent une erreur.

### 6.3 Boucle `for`
```javascript
for (let i = 0; i < 5; i++) {
  console.log(i);
}
```

### 6.4 Boucle `while` et `do...while`
```javascript
let i = 0;
while (i < 5) {
  console.log(i);
  i++;
}

let j = 0;
do {
  console.log(j); // s'exécute au moins une fois
  j++;
} while (j < 5);
```

### 6.5 `for...of` (itère sur les VALEURS d'un itérable : tableau, string, Map, Set...)
```javascript
for (const valeur of ["a", "b", "c"]) {
  console.log(valeur);
}
```

### 6.6 `for...in` (itère sur les CLÉS/propriétés énumérables d'un objet)
```javascript
const personne = { nom: "Awa", age: 25 };
for (const cle in personne) {
  console.log(cle, personne[cle]);
}
```
⚠️ Éviter `for...in` sur les tableaux (préférer `for...of` ou les méthodes de tableau).

### 6.7 `break` et `continue`
```javascript
for (let i = 0; i < 10; i++) {
  if (i === 5) break;     // arrête la boucle
  if (i % 2 === 0) continue; // passe à l'itération suivante
  console.log(i); // affiche 1, 3
}
```

### 6.8 Labels (rare mais utile pour sortir de boucles imbriquées)
```javascript
externe: for (let i = 0; i < 3; i++) {
  for (let j = 0; j < 3; j++) {
    if (j === 1) continue externe;
    console.log(i, j);
  }
}
```

### 🧪 Mini-projet 5 : « Générateur de table de multiplication »
```javascript
function tableDeMultiplication(nombre, max = 10) {
  for (let i = 1; i <= max; i++) {
    console.log(`${nombre} x ${i} = ${nombre * i}`);
  }
}
tableDeMultiplication(7);
```

### 🧪 Mini-projet 6 : « Devine le nombre »
```javascript
const nombreSecret = Math.floor(Math.random() * 100) + 1;
const tentatives = [45, 70, 60, 65, 63];
for (const tentative of tentatives) {
  if (tentative === nombreSecret) {
    console.log(`Trouvé : ${tentative} !`);
    break;
  } else if (tentative < nombreSecret) {
    console.log(`${tentative} est trop petit`);
  } else {
    console.log(`${tentative} est trop grand`);
  }
}
```

---

## 7. Fonctions

### 7.1 Déclaration de fonction (function declaration)
```javascript
function additionner(a, b) {
  return a + b;
}
```
Les déclarations de fonction sont **hoistées entièrement** : on peut les appeler avant leur déclaration dans le code.

### 7.2 Expression de fonction
```javascript
const additionner = function(a, b) {
  return a + b;
};
```
Pas hoistée de la même façon (la variable est hoistée, pas la fonction assignée).

### 7.3 Fonctions fléchées (arrow functions) — ES6
```javascript
const additionner = (a, b) => a + b;              // retour implicite
const carre = (x) => { return x * x; };            // avec accolades = retour explicite
const direBonjour = () => console.log("Bonjour");   // sans paramètre
const identite = x => x;                             // un seul paramètre, parenthèses optionnelles
```

**Différence capitale : le `this`**
Les fonctions fléchées n'ont **pas leur propre `this`** — elles héritent du `this` du contexte englobant (lexical scoping). Les fonctions classiques ont un `this` dynamique déterminé par la façon dont elles sont appelées. (Détails complets dans la partie 2, section POO.)

```javascript
const objet = {
  nom: "Test",
  methodeClassique: function() {
    console.log(this.nom); // "Test" (this = objet)
  },
  methodeFlechee: () => {
    console.log(this.nom); // undefined (this = contexte global)
  }
};
```

### 7.4 Paramètres par défaut
```javascript
function saluer(nom = "Invité") {
  console.log(`Bonjour ${nom}`);
}
saluer();        // "Bonjour Invité"
saluer("Awa");    // "Bonjour Awa"
```

### 7.5 Paramètres du reste (`rest parameters`)
```javascript
function sommeDeTout(...nombres) {
  return nombres.reduce((total, n) => total + n, 0);
}
sommeDeTout(1, 2, 3, 4); // 10
```

### 7.6 L'objet `arguments` (dans les fonctions classiques uniquement)
```javascript
function afficherArguments() {
  console.log(arguments); // objet "array-like", pas un vrai tableau
}
```
⚠️ `arguments` n'existe PAS dans les arrow functions. Préférez le rest parameter `...args`.

### 7.7 Fonctions comme valeurs de première classe (first-class functions)
En JavaScript, les fonctions sont des **valeurs** : on peut les stocker dans des variables, les passer en argument, les retourner d'une autre fonction.

```javascript
function fonctionDeHautNiveau(callback) {
  callback();
}
fonctionDeHautNiveau(() => console.log("Je suis un callback"));
```

### 7.8 Fonctions qui retournent des fonctions (closures — préview, détaillé en Partie 3)
```javascript
function creerCompteur() {
  let compte = 0;
  return function() {
    compte++;
    return compte;
  };
}
const compteur = creerCompteur();
console.log(compteur()); // 1
console.log(compteur()); // 2
```

### 7.9 Fonctions immédiatement invoquées (IIFE)
```javascript
(function() {
  console.log("Exécuté immédiatement");
})();

(() => {
  console.log("Version arrow function");
})();
```
Utilisé historiquement pour créer une portée isolée (avant les modules ES).

### 7.10 Fonctions pures vs impures

- **Pure** : même entrée → même sortie, aucun effet de bord (ne modifie rien à l'extérieur).
```javascript
function pure(a, b) { return a + b; } // pure
```
- **Impure** : dépend ou modifie un état externe.
```javascript
let total = 0;
function impure(a) { total += a; return total; } // impure
```
Les fonctions pures sont plus faciles à tester et à raisonner — pilier de la programmation fonctionnelle.

### 🧪 Mini-projet 7 : « Boîte à outils de fonctions »
```javascript
const estPair = n => n % 2 === 0;
const estPalindrome = str => str === [...str].reverse().join("");
const factorielle = n => n <= 1 ? 1 : n * factorielle(n - 1); // récursion

console.log(estPair(4));              // true
console.log(estPalindrome("kayak"));   // true
console.log(factorielle(5));            // 120
```

### 🧪 Mini-projet 8 : « Gestionnaire de tâches (version console)»
Combine tout ce qui a été vu : variables, tableaux (préview), fonctions, boucles.
```javascript
const taches = [];

function ajouterTache(nom) {
  taches.push({ nom, terminee: false });
  console.log(`Tâche ajoutée : ${nom}`);
}

function terminerTache(index) {
  if (taches[index]) {
    taches[index].terminee = true;
    console.log(`Tâche terminée : ${taches[index].nom}`);
  }
}

function afficherTaches() {
  taches.forEach((tache, i) => {
    console.log(`${i}. [${tache.terminee ? "x" : " "}] ${tache.nom}`);
  });
}

ajouterTache("Apprendre JavaScript");
ajouterTache("Faire un mini-projet");
terminerTache(0);
afficherTaches();
```

---

## ✅ Ce que vous devez maîtriser avant de passer à la Partie 2

- [ ] Différence entre `var`, `let`, `const` et pourquoi `const` par défaut
- [ ] Les 7 types primitifs + le type objet
- [ ] `==` vs `===`, valeurs truthy/falsy
- [ ] Tous les opérateurs, en particulier `??` et `?.`
- [ ] Boucles `for`, `while`, `for...of`, `for...in`
- [ ] Déclarer une fonction de 3 façons différentes (déclaration, expression, arrow)
- [ ] Paramètres par défaut et rest parameters
- [ ] Différence fonction pure / impure

➡️ **Suite : Partie 2 — Tableaux, Objets, Chaînes de caractères, `this` et Programmation Orientée Objet** (fichier `02-structures-donnees.md`)
