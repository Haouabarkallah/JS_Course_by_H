# 📘 Cours complet de JavaScript — Partie 3 : ES6+ et concepts avancés

## Sommaire
1. Closures (fermetures) en profondeur
2. Le scope et la portée lexicale
3. Spread et Rest — récapitulatif complet
4. Modules JavaScript (import/export)
5. `Map`, `Set`, `WeakMap`, `WeakSet`
6. Itérateurs et générateurs
7. Expressions régulières (Regex)
8. JSON
9. Gestion des erreurs
10. Nouveautés récentes (ES2020 → ES2025)
11. Mini-projets

---

## 1. Closures (fermetures) en profondeur

### 1.1 Définition

Une **closure** (fermeture) est une fonction qui "se souvient" de l'environnement (les variables) dans lequel elle a été créée, même après que cet environnement ait normalement cessé d'exister.

```javascript
function creerSalutation(salutation) {
  return function(nom) {
    console.log(`${salutation}, ${nom} !`);
  };
}
const direBonjour = creerSalutation("Bonjour");
const direCoucou = creerSalutation("Coucou");
direBonjour("Awa");   // "Bonjour, Awa !"
direCoucou("Karim");   // "Coucou, Karim !"
```
Chaque fonction retournée "capture" sa propre variable `salutation`.

### 1.2 Cas d'usage concret : compteurs indépendants
```javascript
function creerCompteur() {
  let compte = 0;
  return {
    incrementer: () => ++compte,
    decrementer: () => --compte,
    valeur: () => compte,
  };
}
const compteurA = creerCompteur();
const compteurB = creerCompteur();
compteurA.incrementer();
compteurA.incrementer();
console.log(compteurA.valeur()); // 2
console.log(compteurB.valeur()); // 0 (totalement indépendant)
```

### 1.3 Cas d'usage : encapsulation / données privées (avant les champs `#`)
```javascript
function creerCompteBancaire(soldeInitial) {
  let solde = soldeInitial; // "privé" grâce à la closure
  return {
    deposer(montant) { solde += montant; },
    retirer(montant) {
      if (montant > solde) { console.log("Fonds insuffisants"); return; }
      solde -= montant;
    },
    consulterSolde: () => solde,
  };
}
const compte = creerCompteBancaire(1000);
compte.deposer(500);
console.log(compte.consulterSolde()); // 1500
console.log(compte.solde); // undefined — inaccessible directement !
```

### 1.4 Cas d'usage : fonctions "factory" avec configuration (currying)
```javascript
function multiplierPar(facteur) {
  return nombre => nombre * facteur;
}
const doubler = multiplierPar(2);
const tripler = multiplierPar(3);
console.log(doubler(5), tripler(5)); // 10 15
```

### 1.5 Piège classique des closures dans les boucles (déjà vu Partie 1, ré-expliqué)
```javascript
function creerFonctions() {
  const fonctions = [];
  for (let i = 0; i < 3; i++) {
    fonctions.push(() => console.log(i)); // "let" => chaque itération capture SA propre i
  }
  return fonctions;
}
creerFonctions().forEach(f => f()); // 0, 1, 2
```

### 🧪 Mini-projet 15 : « Générateur de mots de passe avec historique privé »
```javascript
function creerGenerateurMotDePasse() {
  const historique = []; // privé, inaccessible de l'extérieur
  const caracteres = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%";

  return {
    generer(longueur = 12) {
      let motDePasse = "";
      for (let i = 0; i < longueur; i++) {
        motDePasse += caracteres[Math.floor(Math.random() * caracteres.length)];
      }
      historique.push(motDePasse);
      return motDePasse;
    },
    nombreGeneres: () => historique.length,
  };
}
const generateur = creerGenerateurMotDePasse();
console.log(generateur.generer());
console.log(generateur.generer(20));
console.log(generateur.nombreGeneres()); // 2
```

---

## 2. Le scope et la portée lexicale

### 2.1 Trois niveaux de portée
- **Portée globale** : accessible partout.
- **Portée de fonction** : variables déclarées avec `var`, `let`, `const` dans une fonction.
- **Portée de bloc** : `{ }` — pour `let`/`const` uniquement.

### 2.2 Portée lexicale (lexical scoping)
Une fonction a accès aux variables de la portée dans laquelle elle a été **définie** (pas celle d'où elle est appelée).
```javascript
const x = "global";
function externe() {
  const x = "externe";
  function interne() {
    console.log(x); // "externe" — cherche dans son environnement de définition
  }
  interne();
}
externe();
```

### 2.3 Le mode strict
```javascript
"use strict"; // en haut d'un fichier ou d'une fonction
```
Active des règles plus rigoureuses (empêche les variables globales implicites, `this` = `undefined` dans les fonctions simples, erreurs sur affectations invalides, etc.). Les modules ES et les classes sont **automatiquement** en mode strict.

---

## 3. Spread et Rest — récapitulatif complet

### 3.1 Spread `...` (étale des éléments)
```javascript
// Tableaux
const a = [1, 2, 3];
const b = [...a, 4, 5]; // [1, 2, 3, 4, 5]
const max = Math.max(...a); // étale comme arguments

// Objets
const config = { debug: true };
const configEtendue = { ...config, verbose: true };

// Strings
const lettres = [...'salut']; // ['s', 'a', 'l', 'u', 't']

// Copier un tableau (shallow)
const copie = [...a];

// Fusionner des tableaux
const fusion = [...[1, 2], ...[3, 4]];
```

### 3.2 Rest `...` (regroupe des éléments)
```javascript
// Dans les paramètres de fonction
function somme(...nombres) { return nombres.reduce((s, n) => s + n, 0); }

// Dans le destructuring
const [premier, ...autres] = [1, 2, 3, 4];
const { id, ...autresProps } = { id: 1, nom: "Awa", age: 25 };
```

### 🧪 Mini-projet 16 : « Fusion et déduplication de listes »
```javascript
function fusionnerSansDoublons(...tableaux) {
  return [...new Set(tableaux.flat())];
}
console.log(fusionnerSansDoublons([1, 2, 3], [2, 3, 4], [4, 5])); // [1,2,3,4,5]
```

---

## 4. Modules JavaScript (import/export)

Les modules permettent de découper le code en fichiers réutilisables et d'éviter la pollution de l'espace global.

### 4.1 Export nommé
```javascript
// maths.js
export const PI = 3.14159;
export function additionner(a, b) { return a + b; }
export function soustraire(a, b) { return a - b; }

// OU en une seule fois :
// export { PI, additionner, soustraire };
```

### 4.2 Export par défaut (un seul par fichier)
```javascript
// calculatrice.js
export default class Calculatrice {
  additionner(a, b) { return a + b; }
}
```

### 4.3 Importation
```javascript
// main.js
import Calculatrice from "./calculatrice.js";       // export par défaut : nom libre
import { PI, additionner } from "./maths.js";          // exports nommés : noms exacts
import { additionner as add } from "./maths.js";         // renommage
import * as Maths from "./maths.js";                       // importer tout sous un namespace

console.log(Maths.PI);
```

### 4.4 Utiliser les modules dans le navigateur
```html
<script type="module" src="main.js"></script>
```
⚠️ Les modules sont chargés en mode strict, avec leur propre portée, et nécessitent un serveur HTTP (pas `file://`) à cause de CORS.

### 4.5 Modules en Node.js
Deux systèmes coexistent :
```javascript
// CommonJS (historique, encore très utilisé)
const fs = require("fs");
module.exports = { additionner };

// ES Modules (moderne — nécessite "type": "module" dans package.json ou extension .mjs)
import fs from "fs";
export { additionner };
```

### 🧪 Mini-projet 17 : « Bibliothèque d'utilitaires modulaire »
```javascript
// utils/string.js
export const capitaliser = str => str.charAt(0).toUpperCase() + str.slice(1);
export const inverser = str => [...str].reverse().join("");

// utils/math.js
export const estPremier = n => {
  if (n < 2) return false;
  for (let i = 2; i <= Math.sqrt(n); i++) if (n % i === 0) return false;
  return true;
};

// main.js
import { capitaliser, inverser } from "./utils/string.js";
import { estPremier } from "./utils/math.js";
console.log(capitaliser("bonjour"), inverser("bonjour"), estPremier(17));
```

---

## 5. `Map`, `Set`, `WeakMap`, `WeakSet`

### 5.1 `Map` — collection de paires clé-valeur (clés de N'IMPORTE QUEL type)
```javascript
const carte = new Map();
carte.set("nom", "Awa");
carte.set(1, "un");
carte.set(true, "vrai");
const objetCle = {};
carte.set(objetCle, "valeur associée à un objet"); // impossible avec un objet classique !

carte.get("nom");       // "Awa"
carte.has("nom");         // true
carte.delete("nom");
carte.size;                 // nombre d'entrées

for (const [cle, valeur] of carte) {
  console.log(cle, valeur);
}

// Convertir depuis/vers un objet
const map2 = new Map(Object.entries({ a: 1, b: 2 }));
const obj = Object.fromEntries(map2);
```
**Quand utiliser `Map` plutôt qu'un objet ?** Quand les clés ne sont pas forcément des strings, quand l'ordre d'insertion compte de façon fiable, ou quand on ajoute/supprime fréquemment des clés (meilleures performances).

### 5.2 `Set` — collection de valeurs UNIQUES
```javascript
const ensemble = new Set([1, 2, 2, 3, 3, 3]);
console.log(ensemble); // Set(3) {1, 2, 3} — doublons supprimés automatiquement

ensemble.add(4);
ensemble.has(2);      // true
ensemble.delete(1);
ensemble.size;

const tableauUnique = [...new Set([1, 1, 2, 2, 3])]; // dédupliquer un tableau — TRÈS courant
```

### 5.3 `WeakMap` et `WeakSet`
Comme `Map`/`Set`, mais les clés (WeakMap) ou valeurs (WeakSet) doivent être des objets, et sont détenues **faiblement** : elles peuvent être libérées par le ramasse-miettes si elles ne sont plus référencées ailleurs. Utile pour associer des métadonnées à des objets sans provoquer de fuite mémoire (pas d'itération possible, pas de `.size`).

### 🧪 Mini-projet 18 : « Système de tags uniques et compteur de visiteurs uniques »
```javascript
const tagsArticle = new Set();
function ajouterTag(tag) {
  tagsArticle.add(tag.toLowerCase().trim());
}
["JavaScript", "javascript", " Web ", "Web"].forEach(ajouterTag);
console.log([...tagsArticle]); // ["javascript", "web"]

const visiteursUniques = new Map();
function enregistrerVisite(idUtilisateur) {
  visiteursUniques.set(idUtilisateur, (visiteursUniques.get(idUtilisateur) || 0) + 1);
}
[1, 2, 1, 3, 1, 2].forEach(enregistrerVisite);
console.log(visiteursUniques); // Map { 1 => 3, 2 => 2, 3 => 1 }
```

---

## 6. Itérateurs et générateurs

### 6.1 Le protocole itérable
Un objet est **itérable** s'il possède une méthode `[Symbol.iterator]` qui retourne un itérateur (objet avec une méthode `.next()` retournant `{ value, done }`).

```javascript
const iterableManuel = {
  [Symbol.iterator]() {
    let compteur = 0;
    return {
      next() {
        compteur++;
        return compteur <= 3
          ? { value: compteur, done: false }
          : { value: undefined, done: true };
      },
    };
  },
};
for (const valeur of iterableManuel) console.log(valeur); // 1, 2, 3
console.log([...iterableManuel]); // [1, 2, 3]
```
Tableaux, strings, `Map`, `Set` sont nativement itérables — c'est ce qui permet `for...of` et le spread `...`.

### 6.2 Les générateurs (`function*`)
Un générateur est une fonction qui peut être **mise en pause** et **reprise**, produisant une série de valeurs à la demande.

```javascript
function* generateurSimple() {
  yield 1;
  yield 2;
  yield 3;
  return "terminé";
}
const gen = generateurSimple();
console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: "terminé", done: true }

for (const valeur of generateurSimple()) console.log(valeur); // 1, 2, 3 (le "return" n'est pas itéré)
```

### 6.3 Générateurs infinis (très utile !)
```javascript
function* compteurInfini() {
  let n = 0;
  while (true) yield n++;
}
const compteur = compteurInfini();
console.log(compteur.next().value); // 0
console.log(compteur.next().value); // 1

function* fibonacci() {
  let [a, b] = [0, 1];
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}
const fib = fibonacci();
const dixPremiers = Array.from({ length: 10 }, () => fib.next().value);
console.log(dixPremiers); // [0,1,1,2,3,5,8,13,21,34]
```

### 🧪 Mini-projet 19 : « Pagination avec générateur »
```javascript
function* paginer(tableau, taillePage) {
  for (let i = 0; i < tableau.length; i += taillePage) {
    yield tableau.slice(i, i + taillePage);
  }
}
const donnees = Array.from({ length: 23 }, (_, i) => i + 1);
for (const page of paginer(donnees, 5)) {
  console.log(page);
}
```

---

## 7. Expressions régulières (Regex)

### 7.1 Création
```javascript
const regex1 = /motif/flags;
const regex2 = new RegExp("motif", "flags");
```

### 7.2 Flags principaux
- `g` : global (toutes les occurrences)
- `i` : insensible à la casse
- `m` : multiligne
- `s` : le `.` matche aussi les retours à la ligne

### 7.3 Syntaxe essentielle
```javascript
/^abc$/     // ^ début, $ fin
/a.c/        // . = n'importe quel caractère
/a*/          // 0 ou plusieurs
/a+/           // 1 ou plusieurs
/a?/            // 0 ou 1
/a{2,4}/         // entre 2 et 4
/[abc]/           // un caractère parmi a, b, c
/[^abc]/           // un caractère SAUF a, b, c
/[a-z]/              // plage
/\d/ /\D/              // chiffre / non-chiffre
/\w/ /\W/                // caractère de mot / non-mot
/\s/ /\S/                  // espace / non-espace
/(groupe)/                   // groupe de capture
/(?:groupe)/                   // groupe non-capturant
/(?<nom>groupe)/                 // groupe nommé
```

### 7.4 Méthodes courantes
```javascript
const regexEmail = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
regexEmail.test("test@mail.com");        // true — booléen

const texte = "Le chat mange. Le chien mange.";
texte.match(/mange/g);                     // ["mange", "mange"]
texte.matchAll(/(\w+) mange/g);              // itérateur de correspondances détaillées
texte.replace(/chat/, "chien");                // remplace la 1ère occurrence
texte.replace(/mange/g, "dort");                 // remplace toutes
texte.split(/\.\s*/);                              // découpe via regex

// Groupes nommés
const match = "2026-09-14".match(/(?<annee>\d{4})-(?<mois>\d{2})-(?<jour>\d{2})/);
console.log(match.groups.annee); // "2026"
```

### 🧪 Mini-projet 20 : « Validateur de formulaire (email, téléphone, mot de passe fort) »
```javascript
const validateurs = {
  email: str => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(str),
  telephoneCM: str => /^(\+237)?6\d{8}$/.test(str.replace(/\s/g, "")),
  motDePasseFort: str => /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[\W_]).{8,}$/.test(str),
};
console.log(validateurs.email("awa@mail.com"));            // true
console.log(validateurs.telephoneCM("+237699000000"));       // true
console.log(validateurs.motDePasseFort("Azerty123!"));         // true
console.log(validateurs.motDePasseFort("azerty"));                // false
```

---

## 8. JSON (JavaScript Object Notation)

### 8.1 Sérialisation et désérialisation
```javascript
const utilisateur = { nom: "Awa", age: 25, actif: true, tags: ["dev", "js"] };

const chaineJSON = JSON.stringify(utilisateur);
// '{"nom":"Awa","age":25,"actif":true,"tags":["dev","js"]}'

const chaineFormatee = JSON.stringify(utilisateur, null, 2); // indentation lisible

const objetRecupere = JSON.parse(chaineJSON);
```

### 8.2 Limitations importantes
- `undefined`, les fonctions, et les `Symbol` sont **ignorés** lors de `stringify`.
- `Date` est convertie en string ISO ; il faut la reconvertir manuellement après `parse`.
- Les références circulaires provoquent une erreur.

```javascript
JSON.stringify({ a: undefined, b: function(){}, c: 1 }); // '{"c":1}'
```

### 8.3 `replacer` et `reviver`
```javascript
// Filtrer certaines propriétés à l'export
JSON.stringify(utilisateur, ["nom", "age"]); // ne garde que ces clés

// Transformer les valeurs à l'import
const donnees = JSON.parse('{"date":"2026-09-14"}', (cle, valeur) => {
  if (cle === "date") return new Date(valeur);
  return valeur;
});
```

### 🧪 Mini-projet 21 : « Sauvegarde locale de préférences (localStorage + JSON) »
```javascript
function sauvegarderPreferences(preferences) {
  localStorage.setItem("preferences", JSON.stringify(preferences));
}
function chargerPreferences() {
  const donnees = localStorage.getItem("preferences");
  return donnees ? JSON.parse(donnees) : { theme: "clair", langue: "fr" };
}
sauvegarderPreferences({ theme: "sombre", langue: "fr" });
console.log(chargerPreferences());
```

---

## 9. Gestion des erreurs

### 9.1 `try / catch / finally`
```javascript
try {
  const resultat = JSON.parse("{ invalide }");
} catch (erreur) {
  console.error("Erreur de parsing :", erreur.message);
} finally {
  console.log("Toujours exécuté (nettoyage, fermeture de ressources...)");
}
```

### 9.2 Lever ses propres erreurs
```javascript
function diviser(a, b) {
  if (b === 0) {
    throw new Error("Division par zéro impossible");
  }
  return a / b;
}
try {
  diviser(10, 0);
} catch (e) {
  console.error(e.message);
}
```

### 9.3 Classes d'erreurs personnalisées
```javascript
class ErreurValidation extends Error {
  constructor(message, champ) {
    super(message);
    this.name = "ErreurValidation";
    this.champ = champ;
  }
}
function validerAge(age) {
  if (age < 0) throw new ErreurValidation("L'âge ne peut pas être négatif", "age");
}
try {
  validerAge(-5);
} catch (e) {
  if (e instanceof ErreurValidation) {
    console.log(`Erreur sur le champ "${e.champ}" : ${e.message}`);
  } else {
    throw e; // on relance si ce n'est pas le type attendu
  }
}
```

### 9.4 Types d'erreurs natives
`Error`, `TypeError`, `RangeError`, `SyntaxError`, `ReferenceError`, `EvalError`, `URIError`.

### 🧪 Mini-projet 22 : « API de calcul robuste avec erreurs personnalisées »
```javascript
class ErreurCalcul extends Error {
  constructor(message) { super(message); this.name = "ErreurCalcul"; }
}
function calculerRacine(n) {
  if (typeof n !== "number") throw new TypeError("Un nombre est attendu");
  if (n < 0) throw new ErreurCalcul("Impossible de calculer la racine d'un nombre négatif");
  return Math.sqrt(n);
}
[16, -4, "texte"].forEach(valeur => {
  try {
    console.log(`√${valeur} = ${calculerRacine(valeur)}`);
  } catch (e) {
    console.log(`Erreur (${e.name}) : ${e.message}`);
  }
});
```

---

## 10. Nouveautés récentes du langage (ES2020 → ES2025)

| Version | Fonctionnalités clés |
|---|---|
| **ES2020** | `??` (nullish coalescing), `?.` (optional chaining), `BigInt`, `Promise.allSettled`, `globalThis`, `matchAll` |
| **ES2021** | `String.replaceAll`, `??=`/`||=`/`&&=`, `Promise.any`, séparateurs numériques (`1_000_000`) |
| **ES2022** | Champs de classe privés `#`, `Object.hasOwn`, `.at()`, top-level `await`, `Array.findLast`/`findLastIndex`, `Error.cause` |
| **ES2023** | `Array.prototype.toSorted/toReversed/toSpliced/with` (versions non-mutantes !), `findLast`/`findLastIndex` sur tableaux et TypedArrays |
| **ES2024** | `Object.groupBy` / `Map.groupBy`, `Promise.withResolvers`, `ArrayBuffer` resize, `Atomics.waitAsync` |
| **ES2025** | `Set` : `union`, `intersection`, `difference`, `isSubsetOf`... (opérations d'ensembles natives), `RegExp.escape` (proposé), amélioration des imports de modules JSON |

Exemple de méthodes non-mutantes (ES2023), très utiles en programmation fonctionnelle / React :
```javascript
const original = [3, 1, 2];
const trie = original.toSorted();      // [1,2,3] — original INCHANGÉ
const inverse = original.toReversed();  // [2,1,3]
const modifie = original.with(0, 99);    // [99,1,2]
console.log(original); // [3, 1, 2] toujours intact
```

Exemple `Object.groupBy` (ES2024) :
```javascript
const produits = [
  { nom: "Pomme", categorie: "fruit" },
  { nom: "Carotte", categorie: "légume" },
  { nom: "Banane", categorie: "fruit" },
];
const groupes = Object.groupBy(produits, p => p.categorie);
// { fruit: [Pomme, Banane], légume: [Carotte] }
```

Exemple opérations d'ensembles (ES2025) :
```javascript
const a = new Set([1, 2, 3]);
const b = new Set([2, 3, 4]);
console.log(a.union(b));         // Set {1,2,3,4}
console.log(a.intersection(b));   // Set {2,3}
console.log(a.difference(b));      // Set {1}
```

> ℹ️ Pensez à vérifier la [compatibilité navigateur sur MDN (Can I Use)](https://caniuse.com) avant d'utiliser une fonctionnalité très récente en production.

---

## ✅ Ce que vous devez maîtriser avant la Partie 4

- [ ] Comprendre et écrire une closure, et pourquoi elle est utile pour l'encapsulation
- [ ] Portée lexicale vs portée dynamique
- [ ] `import`/`export` nommés et par défaut
- [ ] Différences `Map` vs objet, `Set` pour dédupliquer
- [ ] Écrire un générateur simple avec `yield`
- [ ] Regex de base (email, mot de passe)
- [ ] `JSON.stringify` / `JSON.parse` et leurs limites
- [ ] `try/catch/finally` et erreurs personnalisées

➡️ **Suite : Partie 4 — Le DOM, les événements, la programmation asynchrone (callbacks, Promises, async/await), Fetch API** (fichier `04-asynchrone-dom.md`)
