# Kalami Données — un nouvel espace dans Kalami

## Le projet, en une phrase

Ajouter à kalami-livres.com un troisième espace, à côté de la librairie et de Kalami
Stratégie : un **parcours de révision de la certification Microsoft PL-300**, qui grandit
au rythme de la formation suivie par Claude Marcel, et qui débouchera sur une banque de
questions d'entraînement corrigées par l'IA.

---

## D'où ça vient

Claude Marcel prépare la certification **PL-300 (Microsoft Power BI Data Analyst)**. Il
suit le parcours officiel Microsoft Learn leçon par leçon, et produit au fil de l'eau des
résumés structurés : l'idée centrale d'abord, puis les détails que l'examen vérifie, avec
des analogies et des moyens mnémotechniques.

Ces résumés existaient jusqu'ici dans une conversation et dans un fichier HTML statique
hébergé sur GitHub Pages. Le but de ce projet est de leur donner une vraie maison, dans
Kalami, où ils pourront devenir un outil d'entraînement plutôt qu'un simple document.

---

## Ce que l'espace doit devenir

Cinq étapes, dont les deux premières sont faites.

| Phase | Contenu | État |
|---|---|---|
| 1 | Route, gabarit, carte du parcours | ✅ fait |
| 2 | Migration des résumés de leçons | 🔄 4 sur 25 |
| 3 | Banque de questions, correction automatique, suivi | à venir |
| 4 | Correction enrichie par le coach IA | à venir |
| 5 | Répétition espacée, classes, monétisation éventuelle | à décider |

**La phase 4 est le cœur du projet.** Une banque de QCM corrigés existe déjà chez
plusieurs éditeurs. Ce qui distinguerait celle-ci, c'est que le coach IA — déjà présent
dans `src/lib/strategy/coach` — explique *pourquoi* la réponse choisie était fausse, en
repartant de l'erreur précise commise, au lieu de servir une explication générique.

---

## Pourquoi dans Kalami plutôt qu'ailleurs

Parce que `/strategie` a déjà résolu presque tous les problèmes que pose ce genre
d'espace, et que tout est réutilisable :

| Déjà construit | Réutilisé ici |
|---|---|
| Authentification, comptes partagés | Oui, dès la phase 1 |
| Gabarit d'espace autonome, en-tête et pied de page | Oui, dès la phase 1 |
| Coach IA (SDK Anthropic) | Phase 4 |
| Classes et invitations par jeton | Phase 5, pour des cohortes |
| Stripe et crédits | Phase 5, si monétisation |
| Export DOCX et PDF | Plus tard, pour des fiches de révision |

C'est un deuxième exemplaire du patron `/strategie`, pas un développement à partir de
zéro.

---

## État actuel : ce qui est livré ici

La phase 1 est complète et la phase 2 entamée. Le code a été écrit directement dans le
dépôt Kalami, puis exporté ici parce que la session qui l'a produit n'avait pas le droit
d'écriture sur `cmkouakou/kalami`.

**Il est déjà vérifié** : `npm run typecheck`, `npm run lint` et les 797 tests existants
passaient au moment de l'export. Rien n'a été modifié dans le reste de l'application, à
part trois fichiers décrits plus bas.

### Les fichiers

| Élément | Emplacement |
|---|---|
| Gabarit de l'espace | `src/app/powerbi/layout.tsx` |
| Accueil du parcours | `src/app/powerbi/page.tsx` |
| Page d'une leçon | `src/app/powerbi/[module]/[lecon]/page.tsx` |
| En-tête et pied de page | `src/components/powerbi/powerbi-chrome.tsx` |
| Rendu des blocs de contenu | `src/components/powerbi/lesson-blocks.tsx` |
| Carte du parcours | `src/lib/powerbi/modules.ts` |
| Types des blocs | `src/lib/powerbi/content.ts` |
| Registre des résumés | `src/lib/powerbi/lessons/index.ts` |
| Les 4 résumés rédigés | `src/lib/powerbi/lessons/<module>/<lecon>.ts` |
| Textes français | `src/i18n/fr-powerbi.ts`, `src/i18n/powerbi.ts` |

### Les trois choix de conception à comprendre

**Le contenu est du TypeScript typé, pas du Markdown.** Le dépôt n'a aucun moteur de
rendu Markdown, et en ajouter un pour ça aurait été disproportionné. Une leçon s'écrit
donc avec de petites fabriques déclaratives, dans l'esprit de `src/lib/strategy/steps.ts` :

```ts
titre("Mode de stockage des tables"),
tableau(
  ["Mode", "Comportement"],
  [["Import", "Les requêtes lisent le cache du modèle"]],
),
examen("Les tables calculées n'ont pas de mode de stockage."),
```

Six types de blocs existent : `titre`, `p`, `liste` / `etapes`, `tableau`, `encadre`
(trois tons : à retenir, piège d'examen, moyen mnémotechnique) et `code`.

**Le registre fait foi, pas la carte.** `modules.ts` décrit les 25 leçons du parcours ;
`lessons/index.ts` dit lesquelles ont un résumé écrit. Une leçon sans résumé s'affiche
« à venir » et reste visible, au lieu de provoquer une erreur. Il n'y a donc pas de
drapeau à maintenir en double — ajouter une entrée au registre suffit à publier une leçon.

**L'espace est entièrement privé.** `/powerbi` est dans `PROTECTED_PREFIXES`, chaque page
appelle `requireUser`, et le gabarit demande aux moteurs de recherche de ne pas l'indexer.
C'est volontaire pendant la construction ; l'ouverture au public sera une décision
ultérieure.

---

## Intégrer l'ajout

### Méthode A — le correctif (à essayer en premier)

Depuis la racine du dépôt Kalami, propre et à jour :

```bash
git pull
git am -3 /chemin/vers/kalami-powerbi.patch
```

Le correctif porte son message de commit et les trois modifications de fichiers existants.
En cas d'échec :

```bash
git am --abort
git apply --3way /chemin/vers/kalami-powerbi.patch
```

puis commit manuel. Si cela échoue aussi, méthode B.

### Méthode B — copie et modifications manuelles

**1.** Le dossier `fichiers/` reproduit l'arborescence du dépôt. Ses 14 fichiers sont tous
nouveaux et n'écrasent rien :

```bash
cp -r /chemin/vers/kalami-powerbi/fichiers/src/. <racine-kalami>/src/
```

**2.** Dans `src/lib/auth/routes.ts`, deux ajouts. Dans `PROTECTED_PREFIXES`, après
`"/strategie/compte",` :

```ts
  // Kalami Données reste entièrement privé pendant sa construction
  "/powerbi",
```

Et juste après la constante `STRATEGY_HOME` :

```ts
/** Accueil de Kalami Données (préparation PL-300). */
export const POWERBI_HOME = "/powerbi";
```

**3.** Dans `package.json`, passer `"version"` de `0.16.6` à `0.17.0`. Faire de même dans
`package-lock.json`, aux deux endroits en tête de fichier où la version apparaît — elle y
était restée à `0.15.2`, désynchronisée.

**4.** Dans `CHANGELOG.md`, insérer l'entrée `[v0.17.0]` juste après le titre. Son texte
se trouve dans le correctif, ou peut être reconstruit à partir de la liste de fichiers
ci-dessus.

---

## Vérifier

```bash
npm install
npm run typecheck
npm run lint
npm test
npm run dev
```

Les quatre premières commandes doivent passer. La seule alerte attendue est préexistante,
dans `src/app/opengraph-image.tsx`, sans rapport avec cet ajout.

Ouvrir ensuite `http://localhost:3000/powerbi` **en étant connecté** — une session
anonyme est redirigée vers la connexion, c'est le comportement voulu.

### Ce qui doit s'afficher

- Un en-tête « Kalami Données » avec une pastille « Espace privé »
- Quatre modules, chacun avec ses leçons, leur durée et un lien Microsoft Learn
- Quatre leçons cliquables ; les vingt et une autres, marquées « à venir », visibles mais
  non cliquables
- Sur une leçon : le résumé rendu en blocs, le lien vers la page officielle d'origine, et
  la navigation vers les leçons voisines
- En pied de page, la mention d'absence d'affiliation avec Microsoft

---

## Conventions du dépôt respectées

- En-tête de fichier en français, au format des autres fichiers (Fichier, Projet,
  Description, Auteur, Version, Date, Dépendances)
- Jetons Tailwind maison : `encre`, `paper`, `line`, `sand`, `ink-muted`, `ocre`
- Styles partagés importés depuis `@/components/ui/styles`
- Textes dans un dictionnaire i18n séparé, jamais en dur dans les composants
- Français uniquement pour l'instant, comme le reste du site ; l'anglais reste possible
  plus tard sur le modèle de `src/i18n/`
- `module` n'est jamais un nom de variable locale : la règle ESlint
  `@next/next/no-assign-module-variable` l'interdit

---

## Deux décisions à confirmer avec Claude Marcel

**L'URL.** L'espace est à `/powerbi`, en minuscules, par cohérence avec `/strategie`,
`/livres` et `/auteurs`. Claude Marcel a plusieurs fois écrit `/powerBi` : si c'est bien
ce qu'il veut, il suffit de renommer le dossier `src/app/powerbi` et de changer la
constante `POWERBI_HOME`. À signaler : les URL sont sensibles à la casse, donc avec
`/powerBi`, une personne tapant `/powerbi` tomberait sur une page introuvable.

**Le nom.** « Kalami Données » plutôt que « Kalami Power BI » : brander un produit avec
une marque Microsoft suggèrerait une affiliation, d'où aussi la mention en pied de page.
Si un autre nom est préféré, tout est dans `src/i18n/fr-powerbi.ts`.

---

## Après l'intégration

Supprimer le dossier `kalami-powerbi/` du dépôt `powerbi4you` : il n'est qu'un canal de
transit, ce dépôt servant par ailleurs au guide d'entraînement BRVM.

La suite consiste à migrer les vingt et un résumés restants, module par module, puis à
passer aux phases 3 et 4. La structure est en place pour que chaque nouvelle leçon soit
un seul fichier à écrire et une ligne à ajouter au registre.
