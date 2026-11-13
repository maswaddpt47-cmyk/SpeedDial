# Règles de travail — SpeedDial

Règles communes de collaboration : MD-LIB/collaboration.md, chargé
automatiquement par MD-LIB/CLAUDE.md (04/11/2026). Ici, seulement ce qui est
propre au projet.

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
- Installable en appli (PWA) : le `<head>` du **gabarit** porte `<link rel="manifest">`, `theme-color`, `apple-touch-icon` et le script qui capte `beforeinstallprompt` (`window.__sdInstall`, évènement `sd-install-ready`) et enregistre `sw.js` sur `load` (jamais en `file://`). Le `<head>` extérieur de `index.html` est remplacé au dépaquetage : rien n'y sert. Icônes PNG `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`, `apple-touch-icon.png` (tirées de `icon-512.svg`).
- `sw.js` : réseau d'abord, cache de secours hors ligne. Incrémenter `CACHE` (actuellement `speeddial-v4`) à chaque changement de logique du service worker.
- Aucun raccourci personnel en dur : `DEFAULT` démarre avec une catégorie « Général » vide. Seul le dépôt de sauvegarde (`GH_REPO_DEFAULT`) est en dur, par choix de l'utilisateur.

## Données (localStorage)

- `speeddial_modern_v1` : `{ cats, tiles, pins }` (tuile = `id, name, url, cat, img`).
- `speeddial_modern_v1_theme` (`dark` / `light` / `sable`), `_view` (`grid` / `list` / `compact`), `_collapsed` (catégories repliées), `_gh_repo`, `_gh_token`, `_gh_last`.
- Export / Import JSON : format `{ categories, tiles, pins }` ; l'ancien format (`pinned`, `visits`, `collapsed`, `catColors`) est accepté, les champs inconnus sont ignorés.

## Fonctions

- Tuiles : bouton ✎ toujours visible, étoile cliquable (désépingle), capture d'écran (JPEG ≤ 400×240), glisser-déposer (réordonner / changer de catégorie), recherche par nom, URL ou catégorie.
- Barre du haut (recherche, Éditer, Liste, « + Raccourci ») fixe au défilement : l'ajout d'une tuile reste toujours accessible.
- Sous 820 px la barre latérale est masquée : le bouton « Menu » (barre du haut) ouvre les mêmes actions (catégorie, thème, export/import, sauvegarde, repos).
- Catégories repliables (clic sur le titre ou le chevron). Thèmes Sombre, Clair et Sable (variables CSS `[data-theme]`).
- Bouton « Installer l'appli » (barre latérale et Menu) : visible seulement quand le navigateur propose l'installation (Chrome, Edge, Android) ; disparaît après le choix. Sur iPhone : Partager → « Sur l'écran d'accueil ».
- Vues : Grille (6 colonnes ≥ 1200 px, 4/3/2 en dessous), Liste et Compact (2 colonnes ≥ 800 px).
- Champ « Token GitHub » dans un vrai `<form>` (identifiant fixe caché `speeddial-token-github`, `name="password"`, `autocomplete="current-password"`, bouton « Enregistrer le token » et touche Entrée sans rechargement) pour que Proton Pass le reconnaisse ; enregistrement aussi à chaque frappe. Ne pas le ressortir du formulaire.
- Sauvegarde GitHub (bouton « Sauvegarde ») : dépôt **privé** dédié, token fine-grained limité à ce dépôt (Contents lecture/écriture), fichiers `backups/backup-AAAA-MM-JJ-HHMM.json`, envoi automatique 20 s après chaque modification, 30 jours conservés (le plus récent toujours gardé), restauration depuis la liste.
- Alerte « sauvegarde plus récente sur GitHub » (bannière Restaurer / Ignorer), vérifiée au chargement et au retour sur l'onglet.
- « Mes repos » : importe en tuiles les repos du compte ayant GitHub Pages (hors archivés et hors noms contenant « backup »), en ignorant les URL déjà présentes.

## Déploiement et tests

- Déploiement par GitHub Actions (`.github/workflows/deploy.yml`) à chaque push sur `main`. Un échec « No artifacts named github-pages » est transitoire : relancer le workflow.
- Après déploiement, recharger de force (Ctrl+Maj+R) ; un ancien service worker peut servir une page en cache.
- Tests : Playwright avec `executablePath: '/opt/pw-browsers/chromium'` sur `file://…/index.html`. Le réseau vers GitHub est bloqué dans le sandbox : simuler `api.github.com` avec `page.route`.

## AGORA — contradiction avant décision (installée le 13/11/2026)

Débats dans **`AGORA.md`** (racine) : règles de réponse, gabarit, cycle de
relais. `MD-LIB/agora.md` n'est pas requis pour ouvrir un bloc ni y répondre.

**Ouvrir un bloc dès qu'un de ces faits est constaté** (critères observables,
pas un ressenti) :
1. la décision ferme une porte : format de stockage (`localStorage`, fichier de
   sauvegarde GitHub, export JSON), nouvelle dépendance, contrat entre modules ;
2. deux options envisagées, une seule écrite, sans arbitrage extérieur ;
3. trois itérations sans résolution sur le même problème ;
4. proposition de défaire un existant dont la raison d'être n'est pas retrouvée ;
5. la proposition contredit une note datée (`CLAUDE.md`, `CHANTIERS.md`) ;
6. coût irréversible pour l'usager : perte de données locales, restauration qui
   écrase, rupture de la PWA installée ;
7. le changement touche un calcul ou un chiffre affiché, quelle que soit sa
   taille ; contradicteur Codex, bloc ouvert avant la correction.

Toute « décision à trancher » de `CHANTIERS.md` est candidate : dire en
l'écrivant pourquoi on ouvre un bloc ou non.

**Ne va pas à l'AGORA** : changement de rendu pur ; correctif localisé appuyé
sur une preuve (sauf critère 7) ; tout ce qui se défait en un commit.

**Contradicteur** : session Claude B pour les décisions courantes ; **Codex**
pour la sécurité, le token GitHub, les données personnelles et le critère 7.

**Claude soumet d'office**, sans demander : il écrit le bloc au moment du
choix (pas après le code), le pousse sur `main` (`git pull --rebase origin
main` avant), puis l'annonce en tête de message, ligne à part :
`🔴 **⚖️ AGORA : critère N — <sujet>. Bloc AG-00N commité. → Dis « pull » à
Codex.**` (ou la phrase de relais d'`AGORA.md` pour une session B).
**L'AGORA ne bloque jamais** : le travail continue. L'utilisateur tranche.

## Reprise de session — CHANTIERS.md

Lire **`CHANTIERS.md`** (racine) au démarrage : décisions à trancher, chantiers restants, points à ne pas défaire. Le mettre à jour à chaque avancée significative, pas en fin de session. Une tâche terminée en sort ; ce qui ne doit pas être défait remonte dans sa dernière section.
