# Règles de travail — SpeedDial

## Avant toute intervention

1. `git pull origin main` — obligatoire, sans exception, avant de toucher le moindre fichier.
2. Backup daté de chaque fichier modifié dans `.backups/` (gitignored) :
   - Format : `.backups/<fichier>.<YYYYMMDD-HHMM>`
   - Exemple : `.backups/index.html.20260623-1430`

## Commits

- Un commit par modification distincte.
- Préfixe obligatoire : `feat:`, `fix:`, ou `refactor:`.
- Message clair décrivant ce qui change et pourquoi.

## Push

- Toujours pousser directement sur `main` : `git push origin main`
- Jamais sur une branche intermédiaire sauf instruction explicite.

## Architecture

- `index.html` est un bundle auto-dépaquetant (« Bundled Page ») : runtime + React + ReactDOM + polices en base64, et l'application elle-même dans un `<script type="__bundler/template">` (une seule ligne JSON, ~ligne 382). Ne jamais l'éditer à la main.
- Pour modifier l'application : décoder cette ligne (`json.loads`), remplacer le texte voulu (assertion `count == 1`), ré-encoder avec `json.dumps(t, ensure_ascii=False).replace('</', '<\\u002F')`, puis vérifier qu'un aller-retour sur l'original donne une ligne identique.
- Composant `Component extends DCLogic` : `renderVals()` fournit les valeurs au gabarit (`sc-if`, `sc-for`, `sc-camel-on-<évènement>`). L'ancienne version vanilla de `index.html` reste dans l'historique git (commit `79346d7`).
- `sw.js` : réseau d'abord, cache de secours hors ligne. Incrémenter `CACHE` (actuellement `speeddial-v4`) à chaque changement de logique du service worker.
- Aucune URL personnelle en dur : `DEFAULT` démarre avec une catégorie « Général » vide.

## Données (localStorage)

- `speeddial_modern_v1` : `{ cats, tiles, pins }` (tuile = `id, name, url, cat, img`).
- `speeddial_modern_v1_theme` (`dark` / `light` / `sable`), `_view` (`grid` / `list` / `compact`), `_collapsed` (catégories repliées), `_gh_repo`, `_gh_token`, `_gh_last`.
- Export / Import JSON : format `{ categories, tiles, pins }` ; l'ancien format (`pinned`, `visits`, `collapsed`, `catColors`) est accepté, les champs inconnus sont ignorés.

## Fonctions

- Tuiles : bouton ✎ toujours visible, étoile cliquable (désépingle), capture d'écran (JPEG ≤ 400×240), glisser-déposer (réordonner / changer de catégorie), recherche par nom, URL ou catégorie.
- Sous 820 px la barre latérale est masquée : le bouton « Menu » (barre du haut) ouvre les mêmes actions (catégorie, thème, export/import, sauvegarde, repos).
- Catégories repliables (clic sur le titre ou le chevron). Thèmes Sombre, Clair et Sable (variables CSS `[data-theme]`).
- Vues : Grille (6 colonnes ≥ 1200 px, 4/3/2 en dessous), Liste et Compact (2 colonnes ≥ 800 px).
- Sauvegarde GitHub (bouton « Sauvegarde ») : dépôt **privé** dédié, token fine-grained limité à ce dépôt (Contents lecture/écriture), fichiers `backups/backup-AAAA-MM-JJ-HHMM.json`, envoi automatique 20 s après chaque modification, 30 jours conservés (le plus récent toujours gardé), restauration depuis la liste.
- Alerte « sauvegarde plus récente sur GitHub » (bannière Restaurer / Ignorer), vérifiée au chargement et au retour sur l'onglet.
- « Mes repos » : importe en tuiles les repos du compte ayant GitHub Pages (hors archivés et hors noms contenant « backup »), en ignorant les URL déjà présentes.

## Déploiement et tests

- Déploiement par GitHub Actions (`.github/workflows/deploy.yml`) à chaque push sur `main`. Un échec « No artifacts named github-pages » est transitoire : relancer le workflow.
- Après déploiement, recharger de force (Ctrl+Maj+R) ; un ancien service worker peut servir une page en cache.
- Tests : Playwright avec `executablePath: '/opt/pw-browsers/chromium'` sur `file://…/index.html`. Le réseau vers GitHub est bloqué dans le sandbox : simuler `api.github.com` avec `page.route`.

## Reprise de session — CHANTIERS.md

Lire **`CHANTIERS.md`** (racine) au démarrage : décisions à trancher, chantiers restants, points à ne pas défaire. Le mettre à jour à chaque avancée significative, pas en fin de session. Une tâche terminée en sort ; ce qui ne doit pas être défait remonte dans sa dernière section.
