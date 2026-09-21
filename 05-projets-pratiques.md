# 📘 Cours complet de JavaScript — Partie 5 : Projets pratiques complets

Cette partie rassemble **tout ce qui a été appris** dans des projets complets et fonctionnels (HTML + CSS + JS), plus un chapitre de bonnes pratiques, débogage, et pistes pour la suite.

## Sommaire
1. Projet complet 1 : To-Do List avancée (LocalStorage + filtres + drag & drop léger)
2. Projet complet 2 : Quiz interactif avec minuteur
3. Projet complet 3 : Application météo avec vraie API
4. Projet complet 4 : Mini panier e-commerce
5. Bonnes pratiques & style de code
6. Débogage (DevTools, `debugger`, erreurs courantes)
7. Introduction à Node.js
8. Introduction (brève) au monde des frameworks
9. Feuille de route pour la suite

---

## 1. Projet complet 1 : To-Do List avancée

**Concepts mobilisés** : DOM, événements, délégation, `localStorage`, classes ES6, tableaux (map/filter), template literals.

### `index.html`
```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>To-Do List</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="app">
    <h1>📝 Ma To-Do List</h1>
    <form id="formulaire-tache">
      <input type="text" id="input-tache" placeholder="Nouvelle tâche..." required>
      <button type="submit">Ajouter</button>
    </form>
    <div class="filtres">
      <button data-filtre="toutes" class="actif">Toutes</button>
      <button data-filtre="actives">Actives</button>
      <button data-filtre="terminees">Terminées</button>
    </div>
    <ul id="liste-taches"></ul>
    <p id="stats"></p>
  </div>
  <script src="app.js" defer></script>
</body>
</html>
```

### `app.js`
```javascript
class GestionnaireTaches {
  constructor() {
    this.taches = JSON.parse(localStorage.getItem("taches")) || [];
    this.filtreActuel = "toutes";
  }

  ajouter(texte) {
    this.taches.push({ id: Date.now(), texte, terminee: false });
    this.sauvegarder();
  }

  basculerStatut(id) {
    const tache = this.taches.find(t => t.id === id);
    if (tache) tache.terminee = !tache.terminee;
    this.sauvegarder();
  }

  supprimer(id) {
    this.taches = this.taches.filter(t => t.id !== id);
    this.sauvegarder();
  }

  getTachesFiltrees() {
    if (this.filtreActuel === "actives") return this.taches.filter(t => !t.terminee);
    if (this.filtreActuel === "terminees") return this.taches.filter(t => t.terminee);
    return this.taches;
  }

  getStats() {
    const total = this.taches.length;
    const terminees = this.taches.filter(t => t.terminee).length;
    return { total, terminees, restantes: total - terminees };
  }

  sauvegarder() {
    localStorage.setItem("taches", JSON.stringify(this.taches));
  }
}

const gestionnaire = new GestionnaireTaches();

const listeElement = document.querySelector("#liste-taches");
const formulaire = document.querySelector("#formulaire-tache");
const input = document.querySelector("#input-tache");
const statsElement = document.querySelector("#stats");
const boutonsFiltre = document.querySelectorAll("[data-filtre]");

function rendre() {
  const taches = gestionnaire.getTachesFiltrees();
  listeElement.innerHTML = taches
    .map(t => `
      <li data-id="${t.id}" class="${t.terminee ? "terminee" : ""}">
        <span class="texte-tache">${t.texte}</span>
        <button class="btn-terminer">${t.terminee ? "↩️" : "✅"}</button>
        <button class="btn-supprimer">🗑️</button>
      </li>
    `)
    .join("");

  const { total, terminees, restantes } = gestionnaire.getStats();
  statsElement.textContent = `${total} tâche(s) — ${terminees} terminée(s), ${restantes} restante(s)`;
}

formulaire.addEventListener("submit", (e) => {
  e.preventDefault();
  const texte = input.value.trim();
  if (!texte) return;
  gestionnaire.ajouter(texte);
  input.value = "";
  rendre();
});

listeElement.addEventListener("click", (e) => {
  const li = e.target.closest("li");
  if (!li) return;
  const id = Number(li.dataset.id);
  if (e.target.matches(".btn-terminer")) gestionnaire.basculerStatut(id);
  if (e.target.matches(".btn-supprimer")) gestionnaire.supprimer(id);
  rendre();
});

boutonsFiltre.forEach(bouton => {
  bouton.addEventListener("click", () => {
    boutonsFiltre.forEach(b => b.classList.remove("actif"));
    bouton.classList.add("actif");
    gestionnaire.filtreActuel = bouton.dataset.filtre;
    rendre();
  });
});

rendre(); // affichage initial
```

### `style.css` (basique)
```css
body { font-family: system-ui, sans-serif; background: #f4f4f9; display: flex; justify-content: center; padding: 40px; }
.app { background: white; padding: 24px; border-radius: 12px; width: 400px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
form { display: flex; gap: 8px; margin-bottom: 16px; }
input { flex: 1; padding: 8px; border-radius: 6px; border: 1px solid #ccc; }
button { padding: 8px 12px; border: none; border-radius: 6px; background: #4f46e5; color: white; cursor: pointer; }
.filtres { display: flex; gap: 8px; margin-bottom: 16px; }
.filtres button { background: #e5e7eb; color: #333; }
.filtres button.actif { background: #4f46e5; color: white; }
ul { list-style: none; padding: 0; }
li { display: flex; align-items: center; justify-content: space-between; padding: 8px; border-bottom: 1px solid #eee; }
li.terminee .texte-tache { text-decoration: line-through; color: #999; }
```

---

## 2. Projet complet 2 : Quiz interactif avec minuteur

**Concepts mobilisés** : tableaux d'objets, `setInterval`/`clearInterval`, destructuring, closures, gestion d'état.

```javascript
const questions = [
  { question: "Quelle méthode transforme un tableau ?", options: ["forEach", "map", "sort"], reponse: "map" },
  { question: "Que retourne typeof null ?", options: ["'null'", "'object'", "'undefined'"], reponse: "'object'" },
  { question: "Comment déclare-t-on une constante ?", options: ["var", "let", "const"], reponse: "const" },
];

class Quiz {
  constructor(questions, dureeParQuestion = 15) {
    this.questions = questions;
    this.indexActuel = 0;
    this.score = 0;
    this.dureeParQuestion = dureeParQuestion;
    this.tempsRestant = dureeParQuestion;
    this.intervalId = null;
  }

  demarrer(onMiseAJour, onFin) {
    this.onMiseAJour = onMiseAJour;
    this.onFin = onFin;
    this.demarrerMinuteur();
    this.afficherQuestion();
  }

  demarrerMinuteur() {
    clearInterval(this.intervalId);
    this.tempsRestant = this.dureeParQuestion;
    this.intervalId = setInterval(() => {
      this.tempsRestant--;
      this.onMiseAJour(this.getEtat());
      if (this.tempsRestant <= 0) this.questionSuivante();
    }, 1000);
  }

  repondre(reponseChoisie) {
    const question = this.questions[this.indexActuel];
    if (reponseChoisie === question.reponse) this.score++;
    this.questionSuivante();
  }

  questionSuivante() {
    clearInterval(this.intervalId);
    this.indexActuel++;
    if (this.indexActuel >= this.questions.length) {
      this.onFin(this.score, this.questions.length);
    } else {
      this.demarrerMinuteur();
      this.afficherQuestion();
    }
  }

  afficherQuestion() {
    this.onMiseAJour(this.getEtat());
  }

  getEtat() {
    return {
      question: this.questions[this.indexActuel],
      indexActuel: this.indexActuel,
      total: this.questions.length,
      tempsRestant: this.tempsRestant,
      score: this.score,
    };
  }
}

// Utilisation (version console pour la démo) :
const quiz = new Quiz(questions, 10);
quiz.demarrer(
  (etat) => console.log(`[${etat.tempsRestant}s] Q${etat.indexActuel + 1}: ${etat.question.question}`),
  (score, total) => console.log(`Quiz terminé ! Score : ${score}/${total}`)
);
// Simule une réponse après 2 secondes
setTimeout(() => quiz.repondre("map"), 2000);
```

**Pour l'intégrer dans le HTML**, il suffit de brancher `onMiseAJour` sur des mises à jour du DOM (`innerHTML` de la question, boutons d'options générés dynamiquement avec `map`, écouteurs de clic délégués sur le conteneur de réponses) — exactement comme dans le projet To-Do List ci-dessus.

---

## 3. Projet complet 3 : Application météo avec vraie API

**Concepts mobilisés** : `fetch`, `async/await`, gestion d'erreurs, `AbortController`, debounce (introduit ici).

> Utilisez une clé API gratuite d'un service comme [OpenWeatherMap](https://openweathermap.org/api) ou [WeatherAPI](https://www.weatherapi.com/).

```javascript
// Fonction "debounce" : évite de lancer une requête à chaque frappe clavier
function debounce(fonction, delai) {
  let timeoutId;
  return (...args) => {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fonction(...args), delai);
  };
}

const CLE_API = "VOTRE_CLE_API";
let controleurActuel = null;

async function rechercherMeteo(ville) {
  const divResultat = document.querySelector("#resultat");
  if (!ville) return;

  // Annule la requête précédente si elle est encore en cours
  if (controleurActuel) controleurActuel.abort();
  controleurActuel = new AbortController();

  divResultat.textContent = "Chargement...";
  try {
    const url = `https://api.openweathermap.org/data/2.5/weather?q=${encodeURIComponent(ville)}&appid=${CLE_API}&units=metric&lang=fr`;
    const reponse = await fetch(url, { signal: controleurActuel.signal });
    if (!reponse.ok) throw new Error(reponse.status === 404 ? "Ville introuvable" : "Erreur serveur");
    const donnees = await reponse.json();

    divResultat.innerHTML = `
      <h2>${donnees.name}, ${donnees.sys.country}</h2>
      <p class="temperature">${Math.round(donnees.main.temp)}°C</p>
      <p>${donnees.weather[0].description}</p>
      <p>Humidité : ${donnees.main.humidity}% — Vent : ${donnees.wind.speed} m/s</p>
    `;
  } catch (erreur) {
    if (erreur.name !== "AbortError") {
      divResultat.textContent = `❌ ${erreur.message}`;
    }
  }
}

const inputVille = document.querySelector("#ville");
const rechercheDebattue = debounce((e) => rechercherMeteo(e.target.value.trim()), 600);
inputVille.addEventListener("input", rechercheDebattue);
```

---

## 4. Projet complet 4 : Mini panier e-commerce

**Concepts mobilisés** : classes, `reduce`, `Map`, `localStorage`, délégation d'événements, formatage de devises.

```javascript
class Produit {
  constructor(id, nom, prix, image) {
    Object.assign(this, { id, nom, prix, image });
  }
}

class Panier {
  #articles; // Map<idProduit, { produit, quantite }>

  constructor() {
    this.#articles = new Map(JSON.parse(localStorage.getItem("panier") || "[]"));
  }

  ajouter(produit, quantite = 1) {
    const existant = this.#articles.get(produit.id);
    this.#articles.set(produit.id, {
      produit,
      quantite: (existant?.quantite || 0) + quantite,
    });
    this.#sauvegarder();
  }

  retirer(idProduit) {
    this.#articles.delete(idProduit);
    this.#sauvegarder();
  }

  modifierQuantite(idProduit, quantite) {
    const item = this.#articles.get(idProduit);
    if (!item) return;
    if (quantite <= 0) return this.retirer(idProduit);
    item.quantite = quantite;
    this.#sauvegarder();
  }

  get articles() {
    return [...this.#articles.values()];
  }

  get total() {
    return this.articles.reduce((s, { produit, quantite }) => s + produit.prix * quantite, 0);
  }

  get nombreArticles() {
    return this.articles.reduce((s, { quantite }) => s + quantite, 0);
  }

  #sauvegarder() {
    localStorage.setItem("panier", JSON.stringify([...this.#articles]));
  }
}

function formaterPrix(montant) {
  return new Intl.NumberFormat("fr-FR", { style: "currency", currency: "XAF" }).format(montant);
}

// --- Utilisation ---
const panier = new Panier();
const livre = new Produit(1, "Apprendre JavaScript", 15000);
const souris = new Produit(2, "Souris sans fil", 8000);

panier.ajouter(livre, 2);
panier.ajouter(souris);
console.log(panier.articles);
console.log(`Total : ${formaterPrix(panier.total)}`); // Total : 38 000 XAF
console.log(`Articles : ${panier.nombreArticles}`);
```

`Intl.NumberFormat` (et ses cousins `Intl.DateTimeFormat`, `Intl.RelativeTimeFormat`) sont des API natives puissantes pour l'internationalisation — très utiles et souvent méconnues.

---

## 5. Bonnes pratiques & style de code

### 5.1 Nommage
- Variables/fonctions : `camelCase` descriptif (`calculerTotal`, pas `calc` ou `ct`).
- Classes : `PascalCase` (`GestionnaireTaches`).
- Constantes globales figées : `SCREAMING_SNAKE_CASE` (`MAX_TENTATIVES`).
- Noms de fonctions = verbes (`obtenirUtilisateur`), noms de variables = substantifs.

### 5.2 Éviter les pièges courants
- Toujours `===` / `!==`.
- `const` par défaut, `let` si réassignation nécessaire, jamais `var`.
- Ne pas modifier un tableau/objet qu'on itère.
- Toujours gérer les erreurs des promesses (`.catch` ou `try/catch`).
- Éviter les fonctions à trop de responsabilités (principe de responsabilité unique).
- Éviter la profondeur d'imbrication excessive (extraire en fonctions).
- Toujours valider les entrées utilisateur, surtout avant `innerHTML`.

### 5.3 Outils qualité
- **ESLint** : analyse statique, détecte bugs et incohérences de style.
- **Prettier** : formatage automatique et cohérent du code.
- **JSDoc** : documenter les fonctions avec des commentaires typés.
```javascript
/**
 * Calcule la TVA d'un montant.
 * @param {number} montant - Montant hors taxe
 * @param {number} [taux=0.1925] - Taux de TVA
 * @returns {number} Montant de la TVA
 */
function calculerTVA(montant, taux = 0.1925) {
  return montant * taux;
}
```

### 5.4 Performance
- Minimiser les manipulations DOM directes (batcher via `DocumentFragment`).
- Utiliser la délégation d'événements pour les listes dynamiques.
- `debounce`/`throttle` pour les événements fréquents (`scroll`, `resize`, `input`).
- Éviter les boucles imbriquées inutiles ; préférer `Map`/`Set` pour les recherches (O(1) au lieu de O(n)).

---

## 6. Débogage

### 6.1 `console` en détail
```javascript
console.log("info simple");
console.warn("avertissement");
console.error("erreur");
console.table([{ nom: "Awa", age: 25 }, { nom: "Karim", age: 30 }]); // tableau lisible
console.group("Groupe"); console.log("détail 1"); console.groupEnd();
console.time("chrono"); /* code */ console.timeEnd("chrono"); // mesurer la durée
console.trace(); // affiche la pile d'appels
console.assert(1 === 2, "1 n'est pas égal à 2"); // log uniquement si false
```

### 6.2 Le mot-clé `debugger`
```javascript
function calculer(a, b) {
  debugger; // met le code en pause ici si les DevTools sont ouverts
  return a + b;
}
```

### 6.3 DevTools du navigateur
- Onglet **Sources** : points d'arrêt (breakpoints), exécution pas à pas.
- Onglet **Network** : inspecter les requêtes `fetch`/XHR.
- Onglet **Console** : erreurs, avertissements, exécution interactive.
- Onglet **Application** : inspecter `localStorage`, cookies, cache.

### 6.4 Lire un message d'erreur efficacement
```
Uncaught TypeError: Cannot read properties of undefined (reading 'nom')
    at app.js:42
```
→ Regardez le **type d'erreur** (`TypeError`), le **message** (accès à `.nom` sur `undefined`), et la **ligne** (42). Remontez la pile d'appels (stack trace) pour identifier l'origine.

---

## 7. Introduction à Node.js

Node.js permet d'exécuter du JavaScript côté serveur, hors du navigateur.

```javascript
// server.js — serveur HTTP minimal, sans framework
const http = require("http");

const serveur = http.createServer((requete, reponse) => {
  reponse.writeHead(200, { "Content-Type": "application/json" });
  reponse.end(JSON.stringify({ message: "Bonjour depuis Node.js" }));
});

serveur.listen(3000, () => console.log("Serveur démarré sur http://localhost:3000"));
```

```javascript
// Lire/écrire des fichiers
const fs = require("fs/promises");
async function lireFichier() {
  const contenu = await fs.readFile("data.txt", "utf-8");
  console.log(contenu);
}
```

En pratique, on utilise généralement un framework comme **Express** pour construire des APIs plus facilement :
```javascript
// npm install express
const express = require("express");
const app = express();
app.use(express.json());

app.get("/utilisateurs", (req, res) => res.json([{ id: 1, nom: "Awa" }]));
app.post("/utilisateurs", (req, res) => res.status(201).json(req.body));

app.listen(3000, () => console.log("API démarrée"));
```

---

## 8. Introduction (brève) au monde des frameworks

Une fois le JavaScript "vanilla" (natif) maîtrisé, les frameworks/librairies front-end deviennent naturels :

| Outil | Usage typique |
|---|---|
| **React** | Interfaces basées sur des composants et un DOM virtuel |
| **Vue.js** | Framework progressif, syntaxe déclarative proche du HTML |
| **Svelte** | Compile le code en JS optimisé sans "runtime" virtuel |
| **Angular** | Framework complet, orienté TypeScript, pour grandes applications |
| **Next.js / Nuxt** | Frameworks "meta" par-dessus React/Vue pour le rendu serveur (SSR) |

Ces outils reposent tous sur les fondamentaux que vous venez d'apprendre : fonctions, closures, `this`, événements, promesses, modules, classes. **Rien ne remplace une base JavaScript solide.**

---

## 9. Feuille de route pour la suite

1. ✅ Refaites chaque mini-projet **sans regarder la correction**.
2. ✅ Construisez un projet personnel de A à Z (ex : gestionnaire de budget, journal, blog statique).
3. 📘 Approfondissez : TypeScript (JavaScript typé), tests unitaires (Jest/Vitest), Git/GitHub.
4. 🌐 Explorez les **Web APIs** avancées : `IntersectionObserver`, `Geolocation`, `WebSockets`, `Service Workers` (PWA), `Canvas`/`WebGL`.
5. 🏗️ Apprenez un framework (React est le plus demandé actuellement).
6. 🔁 Contribuez à des projets open source sur GitHub pour lire du code réel.
7. 📚 Gardez [MDN](https://developer.mozilla.org/fr/docs/Web/JavaScript) comme référence quotidienne — même les experts y retournent sans arrêt.

---

## 🎓 Récapitulatif global du cours

| Partie | Fichier | Contenu |
|---|---|---|
| 1 | `01-fondamentaux.md` | Variables, types, opérateurs, structures de contrôle, fonctions |
| 2 | `02-structures-donnees.md` | Tableaux, chaînes, objets, `this`, prototypes, classes/POO |
| 3 | `03-es6-avance.md` | Closures, modules, Map/Set, générateurs, regex, JSON, erreurs, ES2020-2025 |
| 4 | `04-asynchrone-dom.md` | DOM, événements, callbacks, promesses, async/await, event loop, fetch |
| 5 | `05-projets-pratiques.md` | 4 projets complets + bonnes pratiques + débogage + Node.js |

Vous avez maintenant un cours complet couvrant JavaScript **du niveau débutant au niveau avancé**, avec plus de **30 mini-projets pratiques**. La clé de la maîtrise : **pratiquer chaque concept en codant vous-même**, pas seulement en lisant. Bon courage dans votre apprentissage ! 🚀
