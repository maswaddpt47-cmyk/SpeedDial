# CHANTIERS — SpeedDial

État au **07/10/2026**, commit de référence `b323a32` (`main`).

Carnet de reprise : ce qu'une session sans historique doit savoir pour
continuer. Mis à jour à chaque avancée, pas en fin de session. Une tâche
terminée **sort** de ce fichier (son récit est dans `git log`) ; seul ce qui
ne doit pas être défait remonte dans la dernière section.

## Décisions à trancher

- **Couleur de catégorie** (choix de la couleur du point devant le titre,
  fonction de l'ancienne version) : non recréée. La demande de l'utilisateur
  du 07/10/2026 (« une option couleur sable ») a été comprise comme le
  **thème Sable**, livré. À confirmer si une couleur par catégorie est aussi
  voulue.

## Chantiers restants (par priorité)

1. **Vérifier sur le vrai GitHub** (testé seulement avec une fausse API, le
   sandbox n'a pas accès à GitHub) : la bannière « sauvegarde plus récente »
   et la liste de « Mes repos ». Seule la sauvegarde manuelle est confirmée
   par l'utilisateur (07/10/2026).
2. **« Mes repos » avec un token configuré — hypothèse non vérifiée** : avec
   un token, l'import interroge `/user/repos`. Un token à grain fin limité au
   seul dépôt de sauvegarde pourrait ne renvoyer que ce dépôt (exclu par le
   filtre « backup ») et afficher « Aucun repo ». Si l'utilisateur constate
   une liste vide : interroger aussi `/users/<compte>/repos` sans token et
   fusionner.
3. **Barre latérale masquée sous 820 px** : « Nouvelle catégorie »,
   « Sauvegarde » et « Mes repos » sont inaccessibles sur téléphone en
   portrait (le bouton « + Catégorie » de l'en-tête a été retiré à la demande
   de l'utilisateur le 07/10/2026).
4. **Glisser-déposer au toucher** : le glisser-déposer natif du navigateur ne
   marche en général pas sur mobile ; non testé sur téléphone.
5. **Jeton GitHub en `localStorage`** (clé `speeddial_modern_v1_gh_token`) sur
   l'origine `maswaddpt47-cmyk.github.io`, **partagée** avec les autres
   applis du compte : une faille d'injection dans l'une d'elles peut le lire.
   Atténué : jeton à grain fin, limité au dépôt de sauvegarde (Contents
   lecture/écriture), avec expiration à renouveler et à recoller sur chaque
   appareil.
6. **Mineur RGPD** : les favicons passent par `google.com/s2/favicons` (le
   domaine de chaque tuile est transmis à Google). Pas de solution retenue.

## Points à ne pas défaire

- **Compteur de visites : ne pas le recréer** (décision de l'utilisateur,
  07/10/2026). Le champ \`visits\` d'un ancien fichier est ignoré.
- **Aucun nom de compte GitHub en dur** dans l'application (retiré le
  07/10/2026) : le dépôt de sauvegarde est saisi par l'utilisateur, et « Mes
  repos » en déduit le propriétaire.
- **Aucune donnée personnelle dans un repo public ni dans le code** : les
  sauvegardes partent vers un dépôt **privé** dédié (`speeddial-backups`, passé
  en privé le 07/10/2026 après avoir été public un moment : l'historique de
  ses fichiers a pu être vu), et `DEFAULT` démarre vide (catégorie « Général »).
- **Édition de `index.html`** : uniquement via la ligne JSON du gabarit
  (voir `CLAUDE.md`, section Architecture), échappement `<\u002F` obligatoire,
  aller-retour identique vérifié. Un `</script>` brut casse le dépaquetage.
- **`sw.js` en réseau d'abord** (cache v4) : le cache en priorité servait de
  vieilles versions après déploiement (constaté le 07/10/2026).
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
