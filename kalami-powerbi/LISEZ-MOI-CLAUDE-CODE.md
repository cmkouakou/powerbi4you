# Ajouter l'espace `/powerbi` au dépôt Kalami

Ce dossier contient un ajout prêt à intégrer au dépôt **cmkouakou/kalami** : un espace
privé `/powerbi` (« Kalami Données »), parcours de préparation à la certification
Microsoft PL-300.

Il a été écrit dans le dépôt Kalami lui-même, puis déposé ici parce que la session qui
l'a produit n'avait pas le droit d'écriture sur `cmkouakou/kalami`. Le code est donc
déjà vérifié : `npm run typecheck`, `npm run lint` et les 797 tests existants passaient
au moment de l'export.

---

## Ce que l'ajout contient

Un espace bâti sur le même patron que `/strategie` : gabarit propre, en-tête et pied de
page dédiés, connexion et déconnexion qui ramènent à l'espace.

| Élément | Emplacement |
|---|---|
| Gabarit de l'espace | `src/app/powerbi/layout.tsx` |
| Accueil du parcours | `src/app/powerbi/page.tsx` |
| Page d'une leçon | `src/app/powerbi/[module]/[lecon]/page.tsx` |
| En-tête et pied de page | `src/components/powerbi/powerbi-chrome.tsx` |
| Rendu des blocs de contenu | `src/components/powerbi/lesson-blocks.tsx` |
| Carte du parcours (4 modules, 25 leçons) | `src/lib/powerbi/modules.ts` |
| Types des blocs de contenu | `src/lib/powerbi/content.ts` |
| Registre des résumés | `src/lib/powerbi/lessons/index.ts` |
| 4 résumés rédigés | `src/lib/powerbi/lessons/<module>/<lecon>.ts` |
| Textes français | `src/i18n/fr-powerbi.ts`, `src/i18n/powerbi.ts` |

---

## Méthode A — appliquer le correctif (à essayer en premier)

Depuis la racine du dépôt Kalami, le dépôt étant propre et à jour :

```bash
git pull
git am -3 /chemin/vers/kalami-powerbi.patch
```

Le correctif porte déjà son message de commit et les trois modifications de fichiers
existants décrites plus bas. Si `git am` échoue à cause d'une divergence :

```bash
git am --abort
git apply --3way /chemin/vers/kalami-powerbi.patch
```

puis faire le commit manuellement. Si cette seconde commande échoue aussi, passer à la
méthode B.

---

## Méthode B — copier les fichiers et refaire les trois modifications

### 1. Copier les fichiers nouveaux

Le dossier `fichiers/` reproduit l'arborescence du dépôt. Copier son contenu par-dessus
la racine de Kalami : les 14 fichiers sont tous nouveaux, aucun n'écrase d'existant.

```bash
cp -r /chemin/vers/kalami-powerbi/fichiers/src/. <racine-kalami>/src/
```

### 2. Modifier `src/lib/auth/routes.ts`

Deux ajouts. Dans `PROTECTED_PREFIXES`, après `"/strategie/compte",` :

```ts
  // Kalami Données reste entièrement privé pendant sa construction
  "/powerbi",
```

Puis, juste après la constante `STRATEGY_HOME` :

```ts
/** Accueil de Kalami Données (préparation PL-300). */
export const POWERBI_HOME = "/powerbi";
```

### 3. Modifier `package.json`

Passer `"version"` de `0.16.6` à `0.17.0`. Faire la même correction dans
`package-lock.json`, aux deux endroits où la version apparaît en tête de fichier — elle y
était restée à `0.15.2`, désynchronisée.

### 4. Modifier `CHANGELOG.md`

Insérer cette entrée juste après le titre `# Journal des modifications` :

```markdown
## [v0.17.0] - 2026-11-05 — Kalami Données : parcours de préparation PL-300
### Ajouté
- Nouvel espace privé `/powerbi` (« Kalami Données ») : parcours de révision de la
  certification Microsoft PL-300, bâti sur le même patron que Kalami Stratégie — gabarit,
  en-tête et pied de page propres, connexion et déconnexion qui ramènent à l'espace
  (`src/app/powerbi/layout.tsx`, `src/components/powerbi/powerbi-chrome.tsx`).
- Carte du parcours : quatre modules et vingt-cinq leçons, avec durée et lien vers la page
  officielle Microsoft Learn de chacune (`src/lib/powerbi/modules.ts`).
- Résumés de leçons écrits en TypeScript typé plutôt qu'en Markdown, pour rester versionnés
  et relus en revue de code, sans ajouter de dépendance de rendu : blocs titre, paragraphe,
  liste, tableau, encadré et code (`src/lib/powerbi/content.ts`,
  `src/components/powerbi/lesson-blocks.tsx`).
- Quatre premiers résumés rédigés ; une leçon sans résumé reste visible et annoncée comme
  « à venir », le registre faisant foi (`src/lib/powerbi/lessons/`).
- Textes français de l'espace, séparés du dictionnaire principal (`src/i18n/fr-powerbi.ts`).

### Sécurité
- `/powerbi` ajouté aux préfixes protégés : l'espace entier exige une connexion pendant sa
  construction, et son gabarit demande aux moteurs de recherche de ne pas l'indexer
  (`src/lib/auth/routes.ts`).
```

---

## Vérification, quelle que soit la méthode

```bash
npm install
npm run typecheck
npm run lint
npm test
```

Les trois doivent passer. La seule alerte attendue est une alerte préexistante dans
`src/app/opengraph-image.tsx`, sans rapport avec cet ajout.

Puis :

```bash
npm run dev
```

et ouvrir `http://localhost:3000/powerbi` **en étant connecté** — l'espace est privé, une
session non authentifiée est redirigée vers la page de connexion.

### Ce qui doit s'afficher

- Un en-tête « Kalami Données » avec une pastille « Espace privé »
- Quatre modules, dont les leçons sont listées avec leur durée
- Quatre leçons cliquables ; les vingt et une autres, marquées « à venir », restent
  visibles mais non cliquables
- Sur une leçon : le résumé, le lien vers la page Microsoft Learn d'origine, et la
  navigation vers les leçons voisines

---

## Conventions respectées, à conserver pour la suite

- En-tête de fichier en français, au format des autres fichiers du dépôt (Fichier,
  Projet, Description, Auteur, Version, Date, Dépendances)
- Jetons Tailwind maison : `encre`, `paper`, `line`, `sand`, `ink-muted`, `ocre`
- Styles partagés importés depuis `@/components/ui/styles`
- Textes dans un dictionnaire i18n séparé, jamais en dur dans les composants
- `module` n'est jamais utilisé comme nom de variable locale : la règle ESlint
  `@next/next/no-assign-module-variable` l'interdit

---

## Deux points laissés ouverts

**Le nom.** L'espace s'appelle « Kalami Données » plutôt que « Kalami Power BI » :
brander un produit avec une marque Microsoft suggèrerait une affiliation. Le pied de page
porte une mention d'absence d'affiliation. L'URL reste `/powerbi`, en minuscules, par
cohérence avec `/strategie` et `/livres`.

**Pas de manifeste PWA.** Contrairement à `/strategie`, cet espace n'a pas encore son
application installable. À ajouter plus tard si le besoin se confirme, sur le modèle de
`src/app/strategie/manifest.webmanifest/route.ts`.

---

## La suite prévue

Vingt et un résumés restent à migrer depuis la conversation qui les a produits, module par
module. Puis viendront les quiz : banque de questions versionnée dans le dépôt, tentatives
et progression dans Supabase, et correction enrichie par le coach IA déjà présent dans
`src/lib/strategy/coach`.
