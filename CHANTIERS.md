# CHANTIERS — SpeedDial

État au **20/11/2026**, base `6b59330` (`main`) relue par « pull ». Messages d'erreur de la sauvegarde GitHub rendus exacts selon le code de GitHub (voir « Points à ne pas défaire »). Précisions MD-LIB du 13 au 16/11 déjà reportées (`AGENTS.md`, annonce AGORA, rappel de déploiement dans `CLAUDE.md`).

Carnet de reprise : ce qu'une session sans historique doit savoir pour
continuer. Mis à jour à chaque avancée, pas en fin de session. Une tâche
terminée **sort** de ce fichier (son récit est dans `git log`) ; seul ce qui
ne doit pas être défait remonte dans la dernière section.

## Décisions à trancher

Aucune pour l'instant. Chaque décision à trancher est candidate à un bloc
`AGORA.md` (AGORA installée le 13/11/2026, section « AGORA » de `CLAUDE.md`).

## Chantiers restants (par priorité)

1. **Vérifier sur le vrai GitHub** (testé seulement avec une fausse API, le
   sandbox n'a pas accès à GitHub) : la bannière « sauvegarde plus récente »
   (elle n'apparaît qu'après une sauvegarde faite depuis un autre appareil).
   Aussi non vérifié sur le vrai GitHub (20/11/2026) : les messages d'erreur
   401 / 403 / 404 et la sonde `GET /repos/<dépôt>` de la liste (testés avec
   une fausse API seulement ; hypothèse non vérifiée : GitHub répond 200 à
   cette sonde avec un jeton limité à ce dépôt, Metadata étant accordé d'office).
   Confirmés par l'utilisateur le 07/10/2026 : la sauvegarde manuelle et la
   liste de « Mes repos » (avec le token configuré).
2. **Glisser-déposer au toucher** : le glisser-déposer natif du navigateur ne
   marche en général pas sur mobile ; non testé sur téléphone.
3. **Jeton GitHub en `localStorage`** (clé `speeddial_modern_v1_gh_token`) sur
   l'origine `maswaddpt47-cmyk.github.io`, **partagée** avec les autres
   applis du compte : une faille d'injection dans l'une d'elles peut le lire.
   Atténué : jeton à grain fin, limité au dépôt de sauvegarde (Contents
   lecture/écriture), avec expiration à renouveler et à recoller sur chaque
   appareil.
4. **Mineur RGPD** : les favicons passent par `google.com/s2/favicons` (le
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
