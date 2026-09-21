# 📘 Cours complet de JavaScript — Partie 4 : DOM, Événements & Asynchrone

## Sommaire
1. Le DOM (Document Object Model)
2. Sélection et manipulation des éléments
3. Création, modification, suppression d'éléments
4. Les événements
5. Délégation d'événements
6. Formulaires
7. Programmation asynchrone : callbacks
8. Les Promesses (Promise)
9. `async` / `await`
10. La boucle d'événements (Event Loop)
11. Fetch API et communication avec un serveur
12. `localStorage`, `sessionStorage`, cookies
13. Mini-projets

---

## 1. Le DOM (Document Object Model)

Le **DOM** est une représentation en arbre de la page HTML, que JavaScript peut lire et modifier. Ce n'est **pas** une fonctionnalité du langage JavaScript lui-même, mais une **API fournie par le navigateur**.

```
document
 └── html
      ├── head
      │    └── title
      └── body
           ├── h1
           └── div
                └── p
```

`document` est le point d'entrée pour accéder à toute la page.

---

## 2. Sélection et manipulation des éléments

### 2.1 Méthodes de sélection
```javascript
document.getElementById("mon-id");                  // un seul élément
document.getElementsByClassName("ma-classe");          // HTMLCollection (live)
document.getElementsByTagName("p");                       // HTMLCollection (live)

document.querySelector(".ma-classe");                       // premier élément qui matche (sélecteur CSS)
document.querySelectorAll("div.carte");                       // NodeList (statique) de TOUS les éléments
```
👉 **`querySelector` / `querySelectorAll`** sont recommandés aujourd'hui : ils acceptent n'importe quel sélecteur CSS (`.classe`, `#id`, `div > p`, `[data-role="admin"]`, etc.).

### 2.2 Lire et modifier le contenu
```javascript
const titre = document.querySelector("h1");
titre.textContent;           // texte brut (sans HTML) — LECTURE
titre.textContent = "Nouveau titre"; // ÉCRITURE (échappe le HTML, plus sûr)
titre.innerHTML;               // contenu HTML — attention aux failles XSS si contenu utilisateur
titre.innerHTML = "<em>Titre en italique</em>";
```
⚠️ **Sécurité** : n'injectez jamais directement du texte fourni par un utilisateur via `innerHTML` sans l'échapper (risque de XSS). Préférez `textContent` pour du texte simple.

### 2.3 Attributs
```javascript
const lien = document.querySelector("a");
lien.getAttribute("href");
lien.setAttribute("href", "https://exemple.com");
lien.removeAttribute("target");
lien.hasAttribute("href");

// Attributs data-*
const carte = document.querySelector("[data-id]");
carte.dataset.id;          // accès direct via camelCase (data-user-id → dataset.userId)
carte.dataset.userId = "42";
```

### 2.4 Classes CSS
```javascript
const element = document.querySelector(".carte");
element.classList.add("active");
element.classList.remove("hidden");
element.classList.toggle("selectionne");   // ajoute si absent, retire si présent
element.classList.contains("active");         // true/false
element.classList.replace("ancienne", "nouvelle");
```

### 2.5 Styles inline
```javascript
element.style.color = "red";
element.style.backgroundColor = "#222"; // camelCase pour les propriétés composées
element.style.cssText = "color: red; font-size: 20px;"; // plusieurs styles d'un coup

// Lire un style calculé (celui réellement appliqué, y compris via CSS externe)
const styleCalcule = window.getComputedStyle(element);
console.log(styleCalcule.color);
```

### 2.6 Navigation dans le DOM
```javascript
element.parentElement;
element.children;              // enfants ÉLÉMENTS uniquement
element.childNodes;            // enfants (inclut texte, commentaires...)
element.firstElementChild;
element.lastElementChild;
element.nextElementSibling;
element.previousElementSibling;
```

### 🧪 Mini-projet 23 : « Bascule de thème sombre/clair »
```html
<button id="bouton-theme">🌙 / ☀️</button>
```
```javascript
const bouton = document.querySelector("#bouton-theme");
bouton.addEventListener("click", () => {
  document.body.classList.toggle("theme-sombre");
  const estSombre = document.body.classList.contains("theme-sombre");
  localStorage.setItem("theme", estSombre ? "sombre" : "clair");
});
// Charger la préférence au démarrage
if (localStorage.getItem("theme") === "sombre") {
  document.body.classList.add("theme-sombre");
}
```

---

## 3. Création, modification, suppression d'éléments

### 3.1 Créer un élément
```javascript
const div = document.createElement("div");
div.textContent = "Nouvel élément";
div.classList.add("carte");
```

### 3.2 Insérer dans le DOM
```javascript
const conteneur = document.querySelector("#conteneur");
conteneur.appendChild(div);          // ajoute à la fin
conteneur.append(div, "texte brut"); // accepte plusieurs nœuds/texte
conteneur.prepend(div);                // ajoute au début

element.before(nouveauNoeud);            // insère avant l'élément
element.after(nouveauNoeud);               // insère après
element.insertAdjacentHTML("beforeend", "<p>HTML brut</p>"); // performant pour du HTML
```

### 3.3 Modifier / remplacer
```javascript
element.replaceWith(nouvelElement);
ancien.replaceChild(nouveau, ancien2); // méthode plus ancienne
```

### 3.4 Supprimer
```javascript
element.remove();               // moderne, direct
parent.removeChild(element);      // ancienne syntaxe
conteneur.innerHTML = "";           // vider tout le contenu (attention aux listeners perdus)
```

### 3.5 Fragments de document (performance)
```javascript
const fragment = document.createDocumentFragment();
for (let i = 0; i < 100; i++) {
  const li = document.createElement("li");
  li.textContent = `Élément ${i}`;
  fragment.appendChild(li); // pas de reflow à chaque itération
}
document.querySelector("ul").appendChild(fragment); // un seul reflow final
```

### 🧪 Mini-projet 24 : « Liste de courses dynamique »
```html
<input type="text" id="entree" placeholder="Ajouter un article">
<button id="ajouter">Ajouter</button>
<ul id="liste"></ul>
```
```javascript
const entree = document.querySelector("#entree");
const boutonAjouter = document.querySelector("#ajouter");
const liste = document.querySelector("#liste");

function ajouterArticle() {
  const texte = entree.value.trim();
  if (!texte) return;

  const li = document.createElement("li");
  li.textContent = texte;

  const boutonSupprimer = document.createElement("button");
  boutonSupprimer.textContent = "❌";
  boutonSupprimer.addEventListener("click", () => li.remove());

  li.appendChild(boutonSupprimer);
  liste.appendChild(li);
  entree.value = "";
  entree.focus();
}

boutonAjouter.addEventListener("click", ajouterArticle);
entree.addEventListener("keydown", (e) => {
  if (e.key === "Enter") ajouterArticle();
});
```

---

## 4. Les événements

### 4.1 Ajouter un écouteur d'événement
```javascript
element.addEventListener("click", function(event) {
  console.log("Cliqué !", event);
});

// Fonction nommée (permet de retirer l'écouteur plus tard)
function gererClic(event) { console.log("Clic"); }
element.addEventListener("click", gererClic);
element.removeEventListener("click", gererClic);

// Options
element.addEventListener("click", gererClic, { once: true });   // se déclenche une seule fois
element.addEventListener("scroll", gererScroll, { passive: true }); // optimisation perf
```

### 4.2 L'objet `event`
```javascript
element.addEventListener("click", (event) => {
  event.target;          // élément qui a réellement déclenché l'événement
  event.currentTarget;      // élément sur lequel le listener est attaché
  event.type;                 // "click"
  event.preventDefault();       // annule le comportement par défaut (ex: soumission de formulaire)
  event.stopPropagation();        // arrête la propagation (bubbling)
});
```

### 4.3 Événements courants

| Catégorie | Événements |
|---|---|
| Souris | `click`, `dblclick`, `mousedown`, `mouseup`, `mousemove`, `mouseenter`, `mouseleave`, `mouseover`, `mouseout`, `contextmenu` |
| Clavier | `keydown`, `keyup`, `keypress` (déprécié) |
| Formulaire | `submit`, `input`, `change`, `focus`, `blur`, `invalid` |
| Fenêtre/Document | `load`, `DOMContentLoaded`, `resize`, `scroll`, `beforeunload` |
| Tactile | `touchstart`, `touchmove`, `touchend` |
| Souris (drag) | `dragstart`, `dragover`, `drop` |

### 4.4 Bubbling et Capturing (propagation des événements)

Quand un événement se produit sur un élément imbriqué, il se propage en 2 phases :
1. **Capturing** (descend de `window` vers la cible)
2. **Bubbling** (remonte de la cible vers `window`) — comportement par défaut

```javascript
parent.addEventListener("click", () => console.log("parent"));
enfant.addEventListener("click", () => console.log("enfant"));
// Clic sur enfant → affiche "enfant" puis "parent" (bubbling)

element.addEventListener("click", handler, { capture: true }); // écouter en phase de capture
```

### 4.5 `DOMContentLoaded` vs `load`
```javascript
document.addEventListener("DOMContentLoaded", () => {
  console.log("DOM prêt (HTML parsé, sans attendre images/CSS)");
});
window.addEventListener("load", () => {
  console.log("Page ENTIÈREMENT chargée (images, styles, etc.)");
});
```

### 🧪 Mini-projet 25 : « Compteur de clics avec statistiques »
```javascript
let nbClics = 0;
const bouton = document.querySelector("#bouton-magique");
const affichage = document.querySelector("#affichage");

bouton.addEventListener("click", (e) => {
  nbClics++;
  affichage.textContent = `Cliqué ${nbClics} fois. Position : (${e.clientX}, ${e.clientY})`;
});

document.addEventListener("keydown", (e) => {
  if (e.key === "Escape") console.log("Échap pressé — annulation");
});
```

---

## 5. Délégation d'événements

Plutôt que d'attacher un écouteur à chaque élément (coûteux si la liste est longue ou dynamique), on attache **un seul écouteur** au parent, et on utilise `event.target` pour identifier l'élément réellement cliqué.

```javascript
const liste = document.querySelector("#liste-taches");

liste.addEventListener("click", (event) => {
  if (event.target.matches(".bouton-supprimer")) {
    event.target.closest("li").remove();
  }
  if (event.target.matches(".case-cochee")) {
    event.target.closest("li").classList.toggle("terminee");
  }
});
```
**Avantages** : meilleure performance, fonctionne automatiquement pour les éléments ajoutés dynamiquement plus tard (pas besoin de ré-attacher un listener).

`element.closest(selecteur)` : remonte dans les ancêtres jusqu'à trouver une correspondance (très utile en délégation).
`element.matches(selecteur)` : teste si l'élément correspond à un sélecteur CSS.

### 🧪 Mini-projet 26 : « To-Do List complète avec délégation »
```html
<input id="nouvelle-tache" placeholder="Nouvelle tâche">
<button id="ajouter-tache">Ajouter</button>
<ul id="liste-taches"></ul>
```
```javascript
const input = document.querySelector("#nouvelle-tache");
const liste = document.querySelector("#liste-taches");

function creerTache(texte) {
  const li = document.createElement("li");
  li.innerHTML = `
    <span class="texte">${texte}</span>
    <button class="terminer">✅</button>
    <button class="supprimer">🗑️</button>
  `;
  liste.appendChild(li);
}

document.querySelector("#ajouter-tache").addEventListener("click", () => {
  const texte = input.value.trim();
  if (texte) { creerTache(texte); input.value = ""; }
});

// UN SEUL listener pour toute la liste, présente ET future
liste.addEventListener("click", (e) => {
  const li = e.target.closest("li");
  if (!li) return;
  if (e.target.matches(".terminer")) li.classList.toggle("terminee");
  if (e.target.matches(".supprimer")) li.remove();
});
```

---

## 6. Formulaires

```html
<form id="mon-formulaire">
  <input type="text" name="nom" required>
  <input type="email" name="email" required>
  <button type="submit">Envoyer</button>
</form>
```
```javascript
const formulaire = document.querySelector("#mon-formulaire");

formulaire.addEventListener("submit", (event) => {
  event.preventDefault(); // empêche le rechargement de page

  const donnees = new FormData(formulaire);
  const objet = Object.fromEntries(donnees); // { nom: "...", email: "..." }

  console.log(objet);

  if (!formulaire.checkValidity()) {
    console.log("Formulaire invalide");
    return;
  }
});

// Validation en temps réel
formulaire.querySelector("[name=email]").addEventListener("input", (e) => {
  e.target.setCustomValidity(e.target.validity.typeMismatch ? "Email invalide" : "");
});
```

### 🧪 Mini-projet 27 : « Formulaire d'inscription avec validation live »
```javascript
const mdp = document.querySelector("#mot-de-passe");
const confirmation = document.querySelector("#confirmation");
const messageErreur = document.querySelector("#erreur");

function validerFormulaire() {
  if (mdp.value !== confirmation.value) {
    messageErreur.textContent = "Les mots de passe ne correspondent pas";
    return false;
  }
  if (mdp.value.length < 8) {
    messageErreur.textContent = "Le mot de passe doit faire au moins 8 caractères";
    return false;
  }
  messageErreur.textContent = "";
  return true;
}
[mdp, confirmation].forEach(champ => champ.addEventListener("input", validerFormulaire));
```

---

## 7. Programmation asynchrone : les callbacks

### 7.1 JavaScript est mono-thread (single-threaded)

JavaScript exécute une seule instruction à la fois sur un seul thread principal. Pour éviter de "bloquer" la page pendant les opérations longues (réseau, minuteurs, lecture de fichiers), il utilise un modèle **asynchrone non-bloquant** basé sur des callbacks, promesses, et la boucle d'événements.

### 7.2 Callbacks simples
```javascript
function traiterDonnees(donnees, callback) {
  console.log("Traitement en cours...");
  callback(donnees.toUpperCase());
}
traiterDonnees("bonjour", (resultat) => console.log(resultat));
```

### 7.3 Callbacks asynchrones
```javascript
console.log("1. Début");
setTimeout(() => console.log("3. Après 2 secondes"), 2000);
console.log("2. Fin du script synchrone");
// Ordre affiché : 1, 2, puis 3 après 2 secondes
```

### 7.4 Le "Callback Hell" (l'enfer des callbacks)
```javascript
recupererUtilisateur(id, (utilisateur) => {
  recupererCommandes(utilisateur.id, (commandes) => {
    recupererDetails(commandes[0].id, (details) => {
      afficherDetails(details, () => {
        console.log("Terminé"); // code imbriqué illisible, difficile à maintenir
      });
    });
  });
});
```
👉 C'est précisément ce problème que les **Promises** et **async/await** viennent résoudre.

---

## 8. Les Promesses (`Promise`)

### 8.1 Concept

Une `Promise` représente une valeur qui sera disponible **plus tard** (ou une erreur). Elle a 3 états :
- **pending** (en attente)
- **fulfilled** (résolue avec succès)
- **rejected** (rejetée / échec)

### 8.2 Créer une Promise
```javascript
const maPromesse = new Promise((resolve, reject) => {
  const succes = true;
  setTimeout(() => {
    if (succes) {
      resolve("Opération réussie");
    } else {
      reject(new Error("Opération échouée"));
    }
  }, 1000);
});
```

### 8.3 Consommer une Promise avec `.then/.catch/.finally`
```javascript
maPromesse
  .then((resultat) => console.log(resultat))
  .catch((erreur) => console.error(erreur.message))
  .finally(() => console.log("Terminé, succès ou échec"));
```

### 8.4 Chaînage de promesses
```javascript
function attendre(ms, valeur) {
  return new Promise(resolve => setTimeout(() => resolve(valeur), ms));
}

attendre(1000, 5)
  .then(valeur => valeur * 2)     // 10
  .then(valeur => valeur + 1)      // 11
  .then(resultat => console.log(resultat)); // 11
```

### 8.5 Résoudre le "Callback Hell" avec les promesses
```javascript
recupererUtilisateur(id)
  .then(utilisateur => recupererCommandes(utilisateur.id))
  .then(commandes => recupererDetails(commandes[0].id))
  .then(details => afficherDetails(details))
  .catch(erreur => console.error("Erreur dans la chaîne :", erreur))
  .finally(() => console.log("Terminé"));
```

### 8.6 Méthodes statiques importantes

```javascript
// Promise.all : attend TOUTES les promesses ; échoue dès qu'UNE échoue
Promise.all([promesse1, promesse2, promesse3])
  .then(([res1, res2, res3]) => console.log(res1, res2, res3))
  .catch(erreur => console.error("Une promesse a échoué :", erreur));

// Promise.allSettled : attend TOUTES, ne rejette JAMAIS — donne le statut de chacune
Promise.allSettled([promesse1, promesse2]).then(resultats => {
  resultats.forEach(r => console.log(r.status, r.value ?? r.reason));
});

// Promise.race : se résout/rejette dès que LA PREMIÈRE promesse se termine
Promise.race([promesse1, promesse2]).then(premier => console.log(premier));

// Promise.any : se résout dès la PREMIÈRE réussie (ignore les échecs, sauf si toutes échouent)
Promise.any([promesse1, promesse2]).then(premierSucces => console.log(premierSucces));

// Créer une promesse déjà résolue/rejetée
Promise.resolve(42);
Promise.reject(new Error("échec immédiat"));
```

### 🧪 Mini-projet 28 : « Simulateur de chargement de données »
```javascript
function simulerRequete(nom, delai, doitEchouer = false) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      doitEchouer ? reject(new Error(`${nom} a échoué`)) : resolve(`${nom} chargé`);
    }, delai);
  });
}

Promise.all([
  simulerRequete("Utilisateurs", 1000),
  simulerRequete("Produits", 1500),
  simulerRequete("Commandes", 800),
])
  .then(resultats => console.log("Tout est chargé :", resultats))
  .catch(erreur => console.error(erreur.message));

Promise.allSettled([
  simulerRequete("Service A", 500),
  simulerRequete("Service B", 700, true),
]).then(resultats => console.log(resultats));
```

---

## 9. `async` / `await`

### 9.1 Syntaxe de base

`async/await` est du **sucre syntaxique** au-dessus des Promises, qui permet d'écrire du code asynchrone qui **ressemble** à du code synchrone.

```javascript
async function recupererDonnees() {
  console.log("Début");
  const resultat = await attendre(1000, "Données reçues");
  console.log(resultat);
  console.log("Fin");
}
recupererDonnees();
```
Une fonction `async` retourne **toujours** une Promise.

### 9.2 Gestion des erreurs avec `try/catch`
```javascript
async function chargerProfil(id) {
  try {
    const utilisateur = await recupererUtilisateur(id);
    const commandes = await recupererCommandes(utilisateur.id);
    return { utilisateur, commandes };
  } catch (erreur) {
    console.error("Erreur lors du chargement :", erreur.message);
    throw erreur; // on peut relancer si besoin
  } finally {
    console.log("Chargement terminé");
  }
}
```

### 9.3 Exécution séquentielle vs parallèle

```javascript
// ❌ SÉQUENTIEL (lent) — chaque await attend la fin du précédent
async function sequentiel() {
  const a = await attendre(1000, "A");
  const b = await attendre(1000, "B");
  console.log(a, b); // ~2 secondes au total
}

// ✅ PARALLÈLE (rapide) — les deux promesses démarrent en même temps
async function parallele() {
  const [a, b] = await Promise.all([attendre(1000, "A"), attendre(1000, "B")]);
  console.log(a, b); // ~1 seconde au total
}
```

### 9.4 `await` dans une boucle
```javascript
// Séquentiel (un par un)
async function traiterUnParUn(ids) {
  for (const id of ids) {
    const resultat = await recupererDonnees(id);
    console.log(resultat);
  }
}

// Parallèle (tous en même temps)
async function traiterEnParallele(ids) {
  const resultats = await Promise.all(ids.map(id => recupererDonnees(id)));
  console.log(resultats);
}
```

### 9.5 Top-level `await` (ES2022, dans les modules)
```javascript
// Dans un module ES, plus besoin d'englober dans une fonction async
const donnees = await fetch("https://api.exemple.com/data").then(r => r.json());
```

### 🧪 Mini-projet 29 : « Chargeur de profil utilisateur avec gestion d'erreurs »
```javascript
async function chargerProfilComplet(idUtilisateur) {
  try {
    const utilisateur = await simulerRequete(`Utilisateur ${idUtilisateur}`, 500);
    const [commandes, avis] = await Promise.all([
      simulerRequete("Commandes", 700),
      simulerRequete("Avis", 600),
    ]);
    console.log({ utilisateur, commandes, avis });
  } catch (erreur) {
    console.error("Impossible de charger le profil :", erreur.message);
  }
}
chargerProfilComplet(1);
```

---

## 10. La boucle d'événements (Event Loop)

### 10.1 Les acteurs

- **Call Stack (pile d'appels)** : où s'exécute le code synchrone, une frame à la fois.
- **Web APIs** (navigateur) / **APIs C++** (Node) : gèrent `setTimeout`, requêtes réseau, événements DOM, en arrière-plan.
- **Callback Queue / Task Queue (macrotâches)** : `setTimeout`, `setInterval`, événements DOM.
- **Microtask Queue** : callbacks de `Promise` (`.then`, `async/await`), `queueMicrotask`.
- **Event Loop** : boucle qui vérifie sans cesse si la Call Stack est vide, et si oui, y transfère la prochaine tâche en attente — **en donnant toujours priorité aux microtâches** sur les macrotâches.

### 10.2 Exemple pour bien comprendre l'ordre d'exécution
```javascript
console.log("1");

setTimeout(() => console.log("2 (macrotâche)"), 0);

Promise.resolve().then(() => console.log("3 (microtâche)"));

console.log("4");

// Ordre réel : 1, 4, 3, 2
// Le code synchrone (1, 4) passe en premier,
// PUIS toutes les microtâches (3),
// PUIS les macrotâches (2), même avec un délai de 0ms.
```

### 10.3 Pourquoi c'est important
Comprendre l'event loop explique pourquoi `async/await`/Promises sont toujours exécutés **avant** un `setTimeout(fn, 0)`, et pourquoi une boucle synchrone très longue "bloque" toute la page (aucune interaction, aucun rendu possible tant que la Call Stack n'est pas vide).

### 🧪 Mini-projet 30 : « Quiz d'ordre d'exécution »
Prédire puis vérifier l'ordre d'affichage :
```javascript
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
(async () => { console.log("D"); await null; console.log("E"); })();
console.log("F");
// Essayez de prédire l'ordre avant d'exécuter, puis vérifiez !
// Réponse : A, D, F, C, E, B
```

---

## 11. Fetch API et communication avec un serveur

### 11.1 Requête GET simple
```javascript
async function chargerUtilisateurs() {
  try {
    const reponse = await fetch("https://jsonplaceholder.typicode.com/users");
    if (!reponse.ok) {
      throw new Error(`Erreur HTTP : ${reponse.status}`);
    }
    const utilisateurs = await reponse.json();
    console.log(utilisateurs);
  } catch (erreur) {
    console.error("Erreur réseau :", erreur.message);
  }
}
```
⚠️ **Piège classique** : `fetch()` ne rejette PAS la promesse pour un statut HTTP d'erreur (404, 500...). Il faut vérifier `reponse.ok` manuellement !

### 11.2 Requête POST (envoyer des données)
```javascript
async function creerUtilisateur(donnees) {
  const reponse = await fetch("https://jsonplaceholder.typicode.com/users", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(donnees),
  });
  return reponse.json();
}
creerUtilisateur({ nom: "Awa", email: "awa@mail.com" }).then(console.log);
```

### 11.3 PUT, PATCH, DELETE
```javascript
fetch(url, { method: "PUT", headers: {...}, body: JSON.stringify(donnees) });
fetch(url, { method: "PATCH", headers: {...}, body: JSON.stringify(donneesPartielles) });
fetch(url, { method: "DELETE" });
```

### 11.4 Annuler une requête avec `AbortController`
```javascript
const controleur = new AbortController();
fetch(url, { signal: controleur.signal })
  .then(r => r.json())
  .catch(e => { if (e.name === "AbortError") console.log("Requête annulée"); });

setTimeout(() => controleur.abort(), 3000); // annule après 3 secondes
```

### 11.5 Gestion des timeouts
```javascript
async function fetchAvecTimeout(url, ms = 5000) {
  const controleur = new AbortController();
  const timeoutId = setTimeout(() => controleur.abort(), ms);
  try {
    const reponse = await fetch(url, { signal: controleur.signal });
    clearTimeout(timeoutId);
    return reponse;
  } catch (e) {
    throw new Error("La requête a expiré ou a échoué");
  }
}
```

### 🧪 Mini-projet 31 : « Application météo avec Fetch API »
```html
<input id="ville" placeholder="Entrez une ville">
<button id="rechercher">Rechercher</button>
<div id="resultat"></div>
```
```javascript
const inputVille = document.querySelector("#ville");
const boutonRechercher = document.querySelector("#rechercher");
const divResultat = document.querySelector("#resultat");

async function rechercherMeteo(ville) {
  divResultat.textContent = "Chargement...";
  try {
    // Exemple générique — remplacez par une vraie API météo avec clé API
    const reponse = await fetch(`https://api.exemple-meteo.com/data?q=${encodeURIComponent(ville)}`);
    if (!reponse.ok) throw new Error("Ville introuvable");
    const donnees = await reponse.json();
    divResultat.innerHTML = `
      <h2>${donnees.ville}</h2>
      <p>${donnees.temperature}°C — ${donnees.description}</p>
    `;
  } catch (erreur) {
    divResultat.textContent = `Erreur : ${erreur.message}`;
  }
}

boutonRechercher.addEventListener("click", () => {
  const ville = inputVille.value.trim();
  if (ville) rechercherMeteo(ville);
});
```

### 🧪 Mini-projet 32 : « Liste d'articles paginée avec API publique »
```javascript
async function chargerArticles(page = 1) {
  const reponse = await fetch(`https://jsonplaceholder.typicode.com/posts?_page=${page}&_limit=10`);
  const articles = await reponse.json();
  const conteneur = document.querySelector("#articles");
  conteneur.innerHTML = articles
    .map(a => `<article><h3>${a.title}</h3><p>${a.body}</p></article>`)
    .join("");
}
document.querySelector("#page-suivante").addEventListener("click", () => {
  chargerArticles(++pageActuelle);
});
```

---

## 12. `localStorage`, `sessionStorage`, cookies

### 12.1 `localStorage` (persiste même après fermeture du navigateur)
```javascript
localStorage.setItem("cle", "valeur");
localStorage.getItem("cle");
localStorage.removeItem("cle");
localStorage.clear();

// Pour des objets : toujours passer par JSON
localStorage.setItem("utilisateur", JSON.stringify({ nom: "Awa" }));
const utilisateur = JSON.parse(localStorage.getItem("utilisateur"));
```

### 12.2 `sessionStorage` (effacé à la fermeture de l'onglet)
Même API que `localStorage`, mais portée limitée à l'onglet courant.

### 12.3 Cookies (encore utilisés pour l'authentification serveur)
```javascript
document.cookie = "theme=sombre; max-age=3600; path=/";
console.log(document.cookie); // toutes les cookies sous forme de string "cle=valeur; ..."
```
Limites : ~4Ko par cookie, envoyés à chaque requête HTTP (contrairement à `localStorage`).

### 🧪 Mini-projet 33 : « Panier d'achat persistant »
```javascript
class Panier {
  constructor() {
    this.articles = JSON.parse(localStorage.getItem("panier")) || [];
  }
  ajouter(article) {
    this.articles.push(article);
    this.sauvegarder();
  }
  retirer(id) {
    this.articles = this.articles.filter(a => a.id !== id);
    this.sauvegarder();
  }
  sauvegarder() {
    localStorage.setItem("panier", JSON.stringify(this.articles));
  }
  total() {
    return this.articles.reduce((s, a) => s + a.prix, 0);
  }
}
const monPanier = new Panier();
monPanier.ajouter({ id: 1, nom: "Livre JS", prix: 15000 });
console.log(monPanier.total());
```

---

## ✅ Ce que vous devez maîtriser avant la Partie 5

- [ ] Sélectionner/modifier/créer/supprimer des éléments du DOM
- [ ] Ajouter des écouteurs d'événements et comprendre le bubbling
- [ ] Délégation d'événements (`closest`, `matches`)
- [ ] Différence callback / promise / async-await
- [ ] `Promise.all` vs `allSettled` vs `race` vs `any`
- [ ] Écrire une fonction `async` avec `try/catch`
- [ ] Ordre d'exécution : synchrone → microtâches → macrotâches
- [ ] Faire une requête `fetch` GET et POST, avec gestion d'erreurs
- [ ] `localStorage` pour persister des données

➡️ **Suite : Partie 5 — Projets pratiques complets qui combinent TOUT le cours** (fichier `05-projets-pratiques.md`)
