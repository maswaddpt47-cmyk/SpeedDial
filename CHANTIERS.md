# CHANTIERS — SpeedDial

État au **24/11/2026**, base `946399b` (`main`) relue par « pull » (règle 78 ajoutée à `AGENTS.md`). Erreur 422 à la sauvegarde GitHub constatée par l'utilisateur le 24/11/2026 : cause non établie, voir chantier 1.

Carnet de reprise : ce qu'une session sans historique doit savoir pour
continuer. Mis à jour à chaque avancée, pas en fin de session. Une tâche
terminée **sort** de ce fichier (son récit est dans `git log`) ; seul ce qui
ne doit pas être défait remonte dans la dernière section.

## Décisions à trancher

Aucune pour l'instant. Chaque décision à trancher est candidate à un bloc
`AGORA.md` (AGORA installée le 13/11/2026, section « AGORA » de `CLAUDE.md`).

## Chantiers restants (par priorité)

1. **Erreur 422 à « Sauvegarder maintenant » (24/11/2026) — cause non établie.**
   Capture de l'utilisateur : « Sauvegarde impossible : erreur 422 », la liste
   des sauvegardes (dernière : 24/11/2026 à 16:17) s'affichait normalement,
   donc jeton et dépôt étaient bons. L'application ne montrait alors que le
   code. Hypothèses non vérifiées : (a) le fichier de la minute existait déjà
   et l'envoi est parti sans `sha` (GitHub répond 422 « sha wasn't supplied »),
   par ex. deux enregistrements dans la même minute depuis deux appareils ou
   onglets ; (b) une règle du dépôt de sauvegarde (protection de branche) ;
   (c) autre. Le détail renvoyé par GitHub s'affiche désormais après le code
   (`erreur 422 : <message>`) : demander à l'utilisateur le texte exact à la
   prochaine occurrence. Une nouvelle tentative une minute plus tard change le
   nom du fichier. Le dépôt privé n'est pas lisible depuis une session Claude
   (accès non activé) : rien vérifié côté GitHub.

2. **Vérifier sur le vrai GitHub** (testé seulement avec une fausse API, le
   sandbox n'a pas accès à GitHub) : la bannière « sauvegarde plus récente »
   (elle n'apparaît qu'après une sauvegarde faite depuis un autre appareil).
   Aussi non vérifié sur le vrai GitHub (20/11/2026) : les messages d'erreur
   403 / 404 et la sonde `GET /repos/<dépôt>` de la liste (testés avec
   une fausse API seulement, sauf le 401 confirmé par l'utilisateur sur le
   vrai GitHub le 21/11/2026 à 00:22 ; hypothèse non vérifiée : GitHub répond 200 à
   cette sonde avec un jeton limité à ce dépôt, Metadata étant accordé d'office).
   Confirmés par l'utilisateur le 07/10/2026 : la sauvegarde manuelle et la
   liste de « Mes repos » (avec le token configuré).
3. **Glisser-déposer au toucher** : le glisser-déposer natif du navigateur ne
   marche en général pas sur mobile ; non testé sur téléphone.
4. **Jeton GitHub en `localStorage`** (clé `speeddial_modern_v1_gh_token`) sur
   l'origine `maswaddpt47-cmyk.github.io`, **partagée** avec les autres
   applis du compte : une faille d'injection dans l'une d'elles peut le lire.
   Atténué : jeton à grain fin, limité au dépôt de sauvegarde (Contents
   lecture/écriture), avec expiration à renouveler et à recoller sur chaque
   appareil.
5. **Mineur RGPD** : les favicons passent par `google.com/s2/favicons` (le
   domaine de chaque tuile est transmis à Google). Pas de solution retenue.

## Points à ne pas défaire

- **Pas de couleur par catégorie** (décision de l'utilisateur, 07/10/2026) :
  la couleur du point devant le titre reste calculée automatiquement. La
  demande « option couleur sable » concernait le thème Sable, livré.
- **Compteur de visites : ne pas le recréer** (décision de l'utilisateur,
  07/10/2026). Le champ \`visits\` d'un ancien fichier est ignoré.
- **Dépôt de sauvegarde par défaut en dur** (`GH_REPO_DEFAULT` dans le gabarit) :
  voulu par l'utilisateur le 13/10/2026, qui annule le retrait du 07/10/2026
  (saisie jugée inutile). Une valeur saisie dans « Sauvegarde » reste
  prioritaire ; « Mes repos » en déduit le compte.
- **Erreurs de la sauvegarde GitHub exactes selon le code reçu** (20/11/2026) :
  le 20/11/2026, « dépôt introuvable pour ce token » avait envoyé sur une
  fausse piste alors que le jeton collé n'était pas celui du dépôt (proposé
  par Proton Pass, celui d'une autre appli). 401 = jeton refusé, 404 = pas
  d'accès au dépôt ou dépôt inexistant, 403 = droits insuffisants, autre =
  `erreur <code>` (méthode `ghErr`). Pour la liste, un 404 sur `backups` ne
  devient une erreur que si `GET /repos/<dépôt>` échoue aussi : sans cette
  sonde, un dépôt encore vide afficherait à tort « pas d'accès » au premier
  usage. Ne pas remettre un 404 qui retourne `[]` sans cette sonde.
- **Lecture du `sha` sans cache avant d'écraser un fichier** (24/11/2026) :
  l'API Contents répond `Cache-Control: private, max-age=60` (constaté en-tête
  à l'appui) ; un `fetch` par défaut réutilise la réponse pendant 60 s
  (constaté sur serveur local) et enverrait un `sha` périmé. Garder
  `cache: 'no-store'` sur cette lecture. Défaut latent corrigé, non prouvé
  comme cause du 422 du 24/11/2026. Les erreurs hors 401/403/404 affichent
  aussi le texte `message` de GitHub (méthode `ghFail`).
- **Édition de `index.html`** : uniquement via la ligne JSON du gabarit
  (voir `CLAUDE.md`, section Architecture), échappement `<\u002F` obligatoire,
  aller-retour identique vérifié. Un `</script>` brut casse le dépaquetage.
- **`sw.js` en réseau d'abord** (cache v4) : le cache en priorité servait de
  vieilles versions après déploiement (constaté le 07/10/2026).
- **Champ du token dans un `<form>`** (13/11/2026, même correctif que sms-mail-multi `b9ab2bf`) : sans formulaire, `name` ni `autocomplete`, Proton Pass ne propose pas le token. Garder l'identifiant caché hors écran par `left:-9999px`, pas `display:none`. Proton Pass propose bien le token : confirmé par
  l'utilisateur le 13/11/2026.
- **Manifeste et enregistrement du service worker dans le gabarit** (13/11/2026) : depuis la refonte en bundle, ils avaient disparu et l'appli n'était plus installable. Le `<head>` extérieur est remplacé au dépaquetage : ne pas les y remettre. Installation confirmée par l'utilisateur sur son Android le 13/11/2026. Icônes rendues avec la police de secours du sandbox (le « S » n'est pas en Georgia) : à refaire seulement si l'utilisateur le demande.
- **Restauration GitHub** : remplace toutes les données locales, confirmation
  à conserver. L'alerte n'apparaît pas tant que des modifications locales ne
  sont pas encore envoyées (20 s).
- **« Mes repos »** exclut tout repo dont le nom contient « backup »
  (demande du 07/10/2026).
- **Push sur `main` uniquement**, même si la session impose une autre branche
  (`CLAUDE.md`). L'ancien `index.html` vanilla reste dans l'historique
  (commit `79346d7`).

## Pistes

Aucune piste proposée selon la règle des trois pistes maximum : voir les
décisions à trancher ci-dessus.
