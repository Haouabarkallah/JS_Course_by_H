# 📘 Cours complet de JavaScript — Partie 2 : Tableaux, Objets, Chaînes & POO

## Sommaire
1. Les tableaux (Array) et leurs méthodes
2. Les chaînes de caractères (String)
3. Les objets en profondeur
4. `this` en détail
5. Prototypes et héritage prototypal
6. Les classes ES6
7. Mini-projets

---

## 1. Les tableaux (Array)

### 1.1 Création
```javascript
const fruits = ["pomme", "banane", "mangue"];
const vide = [];
const mixte = [1, "deux", true, { trois: 3 }, [4, 5]]; // types mélangés autorisés
const parConstructeur = new Array(1, 2, 3);
const tailleFixe = new Array(5); // tableau de 5 emplacements vides (piège !)
```

### 1.2 Accès et modification
```javascript
fruits[0];               // "pomme"
fruits[fruits.length - 1]; // dernier élément : "mangue"
fruits[1] = "kiwi";        // modification
fruits.length;               // 3
```

### 1.3 Méthodes qui MODIFIENT le tableau (mutation)
```javascript
fruits.push("orange");     // ajoute à la fin, retourne la nouvelle longueur
fruits.pop();               // retire le dernier élément, le retourne
fruits.unshift("fraise");    // ajoute au début
fruits.shift();                // retire le premier élément
fruits.splice(1, 2, "kiwi", "citron"); // supprime/insère à un index donné
fruits.sort();                // trie (par défaut, ordre alphabétique/lexicographique !)
fruits.sort((a, b) => a - b);   // tri numérique correct
fruits.reverse();                // inverse l'ordre
fruits.fill(0, 1, 3);              // remplit avec une valeur entre des index
```

⚠️ **Piège classique du `.sort()` sur les nombres :**
```javascript
[10, 1, 21, 2].sort();          // [1, 10, 2, 21] ❌ (tri lexicographique !)
[10, 1, 21, 2].sort((a, b) => a - b); // [1, 2, 10, 21] ✅
```

### 1.4 Méthodes qui NE MODIFIENT PAS le tableau (retournent une copie/valeur)
```javascript
const nombres = [1, 2, 3, 4, 5];

nombres.slice(1, 3);       // [2, 3] — extrait une portion (ne mute pas)
nombres.concat([6, 7]);      // [1,2,3,4,5,6,7] — fusionne
nombres.join(", ");            // "1, 2, 3, 4, 5" — vers une string
nombres.includes(3);             // true
nombres.indexOf(3);                // 2
nombres.at(-1);                      // 5 (accès depuis la fin, ES2022)
Array.isArray(nombres);                // true
```

### 1.5 Les méthodes fonctionnelles ESSENTIELLES (à maîtriser absolument)

```javascript
const notes = [12, 8, 15, 9, 18, 6];

// forEach : exécute une fonction pour chaque élément (pas de retour utile)
notes.forEach((note, index) => console.log(`Note ${index}: ${note}`));

// map : transforme chaque élément → retourne un NOUVEAU tableau
const notesSur100 = notes.map(note => note * 5);

// filter : garde les éléments qui satisfont une condition → nouveau tableau
const notesAdmises = notes.filter(note => note >= 10);

// reduce : réduit le tableau à UNE seule valeur (accumulateur)
const total = notes.reduce((accumulateur, note) => accumulateur + note, 0);
const moyenne = total / notes.length;

// find : retourne le PREMIER élément qui satisfait une condition
const premiereBonneNote = notes.find(note => note >= 15); // 15

// findIndex : retourne l'INDEX du premier élément trouvé
const indexPremiereBonneNote = notes.findIndex(note => note >= 15); // 4

// some : true si AU MOINS UN élément satisfait la condition
const aUneNoteExcellente = notes.some(note => note >= 18); // true

// every : true si TOUS les éléments satisfont la condition
const tousAdmis = notes.every(note => note >= 10); // false

// flat : aplatit les tableaux imbriqués
[1, [2, 3], [4, [5, 6]]].flat();     // [1, 2, 3, 4, [5, 6]]
[1, [2, [3, [4]]]].flat(Infinity);     // [1, 2, 3, 4]

// flatMap : map() suivi d'un flat(1)
[1, 2, 3].flatMap(x => [x, x * 2]); // [1, 2, 2, 4, 3, 6]
```

### 1.6 Chaînage de méthodes (très courant en JS moderne)
```javascript
const etudiants = [
  { nom: "Awa", note: 15 },
  { nom: "Karim", note: 8 },
  { nom: "Fatou", note: 18 },
  { nom: "Ali", note: 6 },
];

const nomsAdmisEnMajuscule = etudiants
  .filter(e => e.note >= 10)
  .map(e => e.nom.toUpperCase())
  .sort();

console.log(nomsAdmisEnMajuscule); // ["AWA", "FATOU"]
```

### 1.7 Destructuring de tableau (ES6)
```javascript
const [premier, deuxieme, ...reste] = [1, 2, 3, 4, 5];
console.log(premier, deuxieme, reste); // 1 2 [3, 4, 5]

// Échanger deux variables sans variable temporaire
let a = 1, b = 2;
[a, b] = [b, a];

// Ignorer des éléments
const [, deuxiemeSeulement] = [1, 2, 3];
```

### 🧪 Mini-projet 9 : « Analyseur de notes de classe »
```javascript
const notes = [12, 8, 15, 9, 18, 6, 11, 20, 4, 14];

const moyenne = notes.reduce((s, n) => s + n, 0) / notes.length;
const admis = notes.filter(n => n >= 10);
const meilleureNote = Math.max(...notes);   // opérateur spread
const pireNote = Math.min(...notes);

console.log(`Moyenne : ${moyenne.toFixed(2)}`);
console.log(`Admis : ${admis.length}/${notes.length}`);
console.log(`Meilleure note : ${meilleureNote}, pire note : ${pireNote}`);
```

---

## 2. Les chaînes de caractères (String)

### 2.1 Création
```javascript
const s1 = "double quotes";
const s2 = 'simple quotes';
const s3 = `template literal`; // ES6 — permet interpolation et multi-lignes
```

### 2.2 Template literals (littéraux de gabarit) — ES6
```javascript
const nom = "Awa";
const age = 25;
const message = `Bonjour, je m'appelle ${nom} et j'ai ${age} ans.`;

const html = `
  <div>
    <h1>${nom}</h1>
  </div>
`; // multi-lignes natif, sans \n

// Fonctions dans l'interpolation
console.log(`Dans 5 ans j'aurai ${age + 5} ans.`);
```

### 2.3 Propriétés et méthodes essentielles (les strings sont IMMUABLES)
```javascript
const texte = "  Bonjour le Monde  ";

texte.length;                  // longueur
texte.trim();                    // enlève les espaces début/fin
texte.trimStart(); texte.trimEnd();
texte.toUpperCase();               // "  BONJOUR LE MONDE  "
texte.toLowerCase();
texte.includes("Monde");             // true
texte.startsWith("  Bon");             // true
texte.endsWith("de  ");                  // true
texte.indexOf("le");                       // index de la première occurrence
texte.slice(2, 9);                           // extrait "Bonjour"
texte.substring(2, 9);                         // similaire à slice (pas d'index négatifs)
texte.replace("Monde", "JavaScript");            // remplace 1ère occurrence
texte.replaceAll("o", "0");                        // remplace TOUTES les occurrences
texte.split(" ");                                    // ["", "", "Bonjour", "le", "Monde", "", ""]
texte.repeat(2);
texte.padStart(30, "*");                                // rembourrage
texte.padEnd(30, "*");
texte.charAt(2);
texte.at(-1); // dernier caractère (ES2022)
[...texte];    // convertir en tableau de caractères
```

### 2.4 Comparaison et concaténation
```javascript
"a" + "b";        // "ab"
"a".concat("b");   // "ab"
"a" < "b";           // true (comparaison lexicographique par code Unicode)
```

### 🧪 Mini-projet 10 : « Validateur / formateur de texte »
```javascript
function nettoyerNomUtilisateur(entree) {
  return entree
    .trim()
    .toLowerCase()
    .split(" ")
    .filter(mot => mot.length > 0)
    .map(mot => mot[0].toUpperCase() + mot.slice(1))
    .join(" ");
}
console.log(nettoyerNomUtilisateur("   awa   NDIAYE  ")); // "Awa Ndiaye"

function estEmailValide(email) {
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(email);
}
console.log(estEmailValide("test@exemple.com")); // true
console.log(estEmailValide("invalide"));           // false
```

---

## 3. Les objets en profondeur

### 3.1 Création
```javascript
const personne = {
  nom: "Awa",
  age: 25,
  estEtudiant: true,
  adresse: { ville: "Yaoundé", pays: "Cameroun" },
  saluer() {                      // méthode raccourcie (ES6)
    console.log(`Salut, je suis ${this.nom}`);
  },
};

const objetVide = {};
const parConstructeur = new Object();
```

### 3.2 Accès aux propriétés
```javascript
personne.nom;          // notation par point
personne["nom"];         // notation par crochets (utile si clé dynamique)

const cle = "age";
personne[cle];             // 25

personne.saluer();
```

### 3.3 Ajout, modification, suppression
```javascript
personne.email = "awa@mail.com"; // ajout
personne.age = 26;                  // modification
delete personne.estEtudiant;          // suppression
```

### 3.4 Vérifier l'existence d'une propriété
```javascript
"nom" in personne;                  // true
personne.hasOwnProperty("nom");        // true (propriété propre, pas héritée)
Object.hasOwn(personne, "nom");           // true (ES2022, méthode moderne recommandée)
```

### 3.5 Parcourir un objet
```javascript
Object.keys(personne);      // tableau des clés
Object.values(personne);      // tableau des valeurs
Object.entries(personne);       // tableau de paires [clé, valeur]

for (const [cle, valeur] of Object.entries(personne)) {
  console.log(`${cle} : ${valeur}`);
}
```

### 3.6 Raccourcis ES6 (property shorthand & computed keys)
```javascript
const nom = "Awa", age = 25;
const utilisateur = { nom, age }; // équivalent à { nom: nom, age: age }

const cleDynamique = "role";
const objet = { [cleDynamique]: "admin" }; // clé calculée
```

### 3.7 Copie d'objets : spread et `Object.assign`
```javascript
const original = { a: 1, b: 2 };
const copie = { ...original };            // spread — copie SUPERFICIELLE (shallow)
const copie2 = Object.assign({}, original);
const fusion = { ...original, c: 3, a: 10 }; // fusion + écrasement de "a"

// Copie profonde (deep clone)
const copieProfonde = structuredClone(original); // API moderne, gère objets imbriqués
// Alternative : JSON.parse(JSON.stringify(original)) — mais perd les fonctions, Date, etc.
```

⚠️ **Piège : copie superficielle**
```javascript
const obj1 = { infos: { age: 25 } };
const obj2 = { ...obj1 };
obj2.infos.age = 30;
console.log(obj1.infos.age); // 30 !! (même référence pour "infos")
```

### 3.8 Destructuring d'objet
```javascript
const { nom, age, adresse: { ville } } = personne;
const { nom: nomComplet } = personne; // renommage
const { pays = "Inconnu" } = personne.adresse; // valeur par défaut

function afficherProfil({ nom, age }) { // destructuring dans les paramètres
  console.log(`${nom}, ${age} ans`);
}
```

### 3.9 Geler et sceller un objet
```javascript
const config = Object.freeze({ debug: true }); // immuable (superficiel)
config.debug = false; // ignoré silencieusement (erreur en mode strict)

Object.seal(config); // empêche ajout/suppression de propriétés, modification possible
```

### 3.10 Getters et Setters
```javascript
const compte = {
  _solde: 1000,
  get solde() {
    return `${this._solde} FCFA`;
  },
  set solde(valeur) {
    if (valeur < 0) throw new Error("Le solde ne peut pas être négatif");
    this._solde = valeur;
  },
};
console.log(compte.solde); // "1000 FCFA" (appelé comme une propriété, pas une méthode)
compte.solde = 2000;
```

### 🧪 Mini-projet 11 : « Carnet d'adresses »
```javascript
const carnet = {
  contacts: [],
  ajouter({ nom, telephone }) {
    this.contacts.push({ nom, telephone, id: Date.now() });
  },
  rechercher(nom) {
    return this.contacts.filter(c => c.nom.toLowerCase().includes(nom.toLowerCase()));
  },
  supprimer(id) {
    this.contacts = this.contacts.filter(c => c.id !== id);
  },
};
carnet.ajouter({ nom: "Awa", telephone: "699000000" });
carnet.ajouter({ nom: "Karim", telephone: "677111111" });
console.log(carnet.rechercher("awa"));
```

---

## 4. `this` en détail

`this` fait référence à l'objet qui "possède" le code en cours d'exécution. Sa valeur dépend du **contexte d'appel**, pas de l'endroit où la fonction est définie (sauf pour les arrow functions).

### 4.1 Les 4 règles de base

**1. Appel en tant que méthode** → `this` = l'objet avant le point
```javascript
const chien = {
  nom: "Rex",
  aboyer() { console.log(`${this.nom} aboie`); },
};
chien.aboyer(); // "Rex aboie"
```

**2. Appel simple de fonction** → `this` = `undefined` (mode strict) ou `window`/`global` (mode non-strict)
```javascript
function testerThis() { console.log(this); }
testerThis(); // undefined en mode strict
```

**3. Appel avec `new`** → `this` = le nouvel objet créé
```javascript
function Personne(nom) { this.nom = nom; }
const p = new Personne("Awa"); // this = p
```

**4. Appel explicite avec `call`, `apply`, `bind`**
```javascript
function saluer() { console.log(`Bonjour ${this.nom}`); }
const utilisateur = { nom: "Awa" };

saluer.call(utilisateur);           // appel immédiat, this = utilisateur
saluer.apply(utilisateur);            // comme call, mais arguments en tableau
const saluerAwa = saluer.bind(utilisateur); // retourne une NOUVELLE fonction liée
saluerAwa();
```

### 4.2 Le piège classique : perte de `this`
```javascript
const chien = {
  nom: "Rex",
  aboyer() { console.log(`${this.nom} aboie`); },
};
const fonctionSeule = chien.aboyer;
fonctionSeule(); // "undefined aboie" — this a été perdu !

setTimeout(chien.aboyer, 1000); // même problème
```

**Solutions :**
```javascript
setTimeout(() => chien.aboyer(), 1000);        // arrow function englobante
setTimeout(chien.aboyer.bind(chien), 1000);      // bind
```

### 4.3 `this` dans les arrow functions
Les arrow functions n'ont pas de `this` propre : elles utilisent celui du contexte lexical (englobant) au moment de leur DÉFINITION.
```javascript
const minuteur = {
  secondes: 0,
  demarrer() {
    setInterval(() => {
      this.secondes++; // "this" = minuteur, car hérité de demarrer()
      console.log(this.secondes);
    }, 1000);
  },
};
minuteur.demarrer();
```
👉 C'est exactement pour cette raison que les arrow functions sont très utilisées comme callbacks à l'intérieur de méthodes.

### 🧪 Mini-projet 12 : « Chronomètre »
```javascript
const chrono = {
  secondes: 0,
  intervalId: null,
  demarrer() {
    this.intervalId = setInterval(() => {
      this.secondes++;
      console.log(`${this.secondes}s`);
    }, 1000);
  },
  arreter() {
    clearInterval(this.intervalId);
    console.log(`Arrêté à ${this.secondes}s`);
  },
};
chrono.demarrer();
setTimeout(() => chrono.arreter(), 5000);
```

---

## 5. Prototypes et héritage prototypal

### 5.1 Chaque objet a un prototype

JavaScript utilise l'**héritage prototypal** : chaque objet possède un lien caché (`[[Prototype]]`, accessible via `__proto__` ou `Object.getPrototypeOf`) vers un autre objet, dont il hérite les propriétés/méthodes.

```javascript
const animal = {
  manger() { console.log(`${this.nom} mange`); },
};
const chien = Object.create(animal); // chien hérite de animal
chien.nom = "Rex";
chien.manger(); // "Rex mange" (méthode trouvée dans le prototype)
```

### 5.2 La chaîne de prototypes (prototype chain)
```javascript
const arr = [1, 2, 3];
// arr → Array.prototype → Object.prototype → null
console.log(arr.__proto__ === Array.prototype); // true
console.log(Array.prototype.__proto__ === Object.prototype); // true
```
Quand on accède à `arr.push`, JS cherche d'abord sur `arr`, ne trouve pas, remonte à `Array.prototype`, trouve `push` là.

### 5.3 Fonctions constructeurs (façon pré-ES6, bon à connaître)
```javascript
function Personne(nom, age) {
  this.nom = nom;
  this.age = age;
}
Personne.prototype.saluer = function() {
  console.log(`Bonjour, je suis ${this.nom}`);
};
const p1 = new Personne("Awa", 25);
p1.saluer();
```
`new` fait 4 choses : (1) crée un objet vide, (2) lie son prototype au `.prototype` de la fonction, (3) exécute la fonction avec `this` = ce nouvel objet, (4) retourne l'objet (sauf si la fonction retourne explicitement un autre objet).

### 🧪 Mini-projet 13 : « Hiérarchie d'animaux (façon prototype) »
```javascript
function Animal(nom) { this.nom = nom; }
Animal.prototype.presenter = function() { console.log(`Je suis ${this.nom}`); };

function Chien(nom, race) {
  Animal.call(this, nom); // héritage des propriétés
  this.race = race;
}
Chien.prototype = Object.create(Animal.prototype); // héritage des méthodes
Chien.prototype.constructor = Chien;
Chien.prototype.aboyer = function() { console.log(`${this.nom} aboie`); };

const rex = new Chien("Rex", "Berger");
rex.presenter(); // hérité d'Animal
rex.aboyer();
```

---

## 6. Les classes ES6 (syntaxe moderne au-dessus des prototypes)

### 6.1 Syntaxe de base
```javascript
class Animal {
  constructor(nom) {
    this.nom = nom;
  }
  presenter() {
    console.log(`Je suis ${this.nom}`);
  }
}
const animal = new Animal("Rex");
animal.presenter();
```
👉 Une classe ES6 est en réalité du **sucre syntaxique** au-dessus des prototypes vus ci-dessus.

### 6.2 Héritage avec `extends` et `super`
```javascript
class Chien extends Animal {
  constructor(nom, race) {
    super(nom);       // appelle le constructeur parent — OBLIGATOIRE avant d'utiliser "this"
    this.race = race;
  }
  aboyer() {
    console.log(`${this.nom} aboie (${this.race})`);
  }
  presenter() {          // redéfinition (override) d'une méthode parente
    super.presenter();    // appelle la version parente
    console.log(`Je suis aussi un chien de race ${this.race}`);
  }
}
const rex = new Chien("Rex", "Berger");
rex.aboyer();
rex.presenter();
```

### 6.3 Propriétés et méthodes statiques
```javascript
class MathUtils {
  static PI = 3.14159;
  static carre(x) { return x * x; }
}
console.log(MathUtils.PI, MathUtils.carre(4)); // pas besoin d'instance !
```

### 6.4 Champs privés (ES2022) — vraie encapsulation
```javascript
class CompteBancaire {
  #solde; // champ privé — inaccessible depuis l'extérieur

  constructor(soldeInitial) {
    this.#solde = soldeInitial;
  }
  deposer(montant) {
    this.#solde += montant;
  }
  get solde() { return this.#solde; }

  #calculerInterets() { // méthode privée
    return this.#solde * 0.02;
  }
  appliquerInterets() {
    this.#solde += this.#calculerInterets();
  }
}
const compte = new CompteBancaire(1000);
compte.deposer(500);
console.log(compte.solde); // 1500
console.log(compte.#solde); // ❌ SyntaxError : propriété privée inaccessible
```

### 6.5 Getters / setters dans une classe
```javascript
class Rectangle {
  constructor(largeur, hauteur) {
    this.largeur = largeur;
    this.hauteur = hauteur;
  }
  get aire() { return this.largeur * this.hauteur; }
  get perimetre() { return 2 * (this.largeur + this.hauteur); }
}
const rect = new Rectangle(5, 3);
console.log(rect.aire, rect.perimetre);
```

### 6.6 `instanceof` et vérification de type
```javascript
console.log(rex instanceof Chien);  // true
console.log(rex instanceof Animal);  // true (héritage)
console.log(rex instanceof Object);   // true (tout hérite d'Object)
```

### 6.7 Les 4 piliers de la POO en JavaScript

| Pilier | Explication | En JS |
|---|---|---|
| **Encapsulation** | Cacher les détails internes | Champs privés `#`, closures |
| **Héritage** | Réutiliser du code d'une classe parente | `extends`, `super` |
| **Polymorphisme** | Une même méthode se comporte différemment selon l'objet | Redéfinition de méthodes (override) |
| **Abstraction** | Exposer seulement l'essentiel | Classes/méthodes bien nommées, interfaces implicites |

### 🧪 Mini-projet 14 : « Système de gestion de véhicules » (POO complète)
```javascript
class Vehicule {
  #kilometrage = 0;
  constructor(marque, modele) {
    this.marque = marque;
    this.modele = modele;
  }
  rouler(km) {
    this.#kilometrage += km;
    console.log(`${this.marque} ${this.modele} a roulé ${km} km`);
  }
  get kilometrage() { return this.#kilometrage; }
  presenter() {
    return `${this.marque} ${this.modele} — ${this.#kilometrage} km`;
  }
}

class Voiture extends Vehicule {
  constructor(marque, modele, nbPortes) {
    super(marque, modele);
    this.nbPortes = nbPortes;
  }
  presenter() {
    return `${super.presenter()} — ${this.nbPortes} portes`;
  }
}

class Moto extends Vehicule {
  presenter() {
    return `${super.presenter()} — 2 roues 🏍️`;
  }
}

const flotte = [
  new Voiture("Toyota", "Corolla", 4),
  new Moto("Yamaha", "MT-07"),
];

flotte.forEach(v => {
  v.rouler(100);
  console.log(v.presenter()); // polymorphisme : chaque presenter() est différent
});
```

---

## ✅ Ce que vous devez maîtriser avant la Partie 3

- [ ] Toutes les méthodes de tableau (map/filter/reduce/find/some/every)
- [ ] Différence méthode mutante vs non-mutante sur les tableaux
- [ ] Destructuring (tableau ET objet)
- [ ] Spread/copie superficielle vs profonde
- [ ] Les 4 règles de `this`
- [ ] Prototype chain
- [ ] Classes ES6 : `extends`, `super`, champs privés, statiques

➡️ **Suite : Partie 3 — ES6+, Closures, Modules, Itérateurs/Générateurs, Map/Set, Regex, JSON** (fichier `03-es6-avance.md`)
