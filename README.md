# PowerBI4You — Chantier BRVM Power BI

## 📋 Description
Guide interactif, en français, pour apprendre à construire un dashboard Power BI de A à Z à
partir de vraies données boursières (schéma `reporting` de BRVM Analyser). Le guide est organisé
en 7 sprints progressifs, avec des checklists à cocher pour suivre sa progression.

## 🚀 Fonctionnalités
- 7 sprints : vue d'ensemble & chargement, nettoyage, transformation, modélisation, DAX avancé,
  présentation, finalisation
- Notions clés expliquées en encadrés rétractables (vue SQL, schéma en étoile, DAX)
- Checklists de progression par sprint, avec anneau de progression global
- Progression sauvegardée automatiquement (voir *Notes importantes*)

## 🛠️ Technologies utilisées
- HTML5 / CSS3 / JavaScript vanilla (page unique, aucune dépendance de build)

## 📁 Structure des fichiers
```
powerbi4you/
├── index.html   → Le guide complet (page unique)
└── README.md
```

## ⚙️ Installation
1. Cloner ce dépôt
2. Ouvrir `index.html` dans un navigateur — aucune installation ni serveur requis

## 📌 Notes importantes
- Ce guide provient à l'origine d'un Artifact Claude, où la progression est synchronisée sur le
  compte claude.ai de l'utilisateur (capacité `db`). Hors de ce contexte (ex. hébergé seul sur
  GitHub Pages), la page bascule automatiquement sur `localStorage` du navigateur — la
  progression reste alors propre à chaque navigateur/appareil.
- Contenu pédagogique conçu pour un chantier d'entraînement personnel ; les 7 vues couvertes ne
  représentent qu'une partie du schéma `reporting` complet.

## 📅 Historique des versions
| Version | Date       | Changements          |
|---------|------------|-----------------------|
| 1.0     | 2026-09-15 | Import initial depuis l'Artifact Claude (7 sprints + fix persistance) |

## 👤 Auteur
Claude Marcel Kouakou
