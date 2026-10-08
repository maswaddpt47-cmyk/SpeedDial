# CHANTIERS — SpeedDial

État au **07/10/2026**, commit de référence `b323a32` (`main`).

Carnet de reprise : ce qu'une session sans historique doit savoir pour
continuer. Mis à jour à chaque avancée, pas en fin de session. Une tâche
terminée **sort** de ce fichier (son récit est dans `git log`) ; seul ce qui
ne doit pas être défait remonte dans la dernière section.

## Décisions à trancher

Aucune pour l'instant.

## Chantiers restants (par priorité)

1. **Vérifier sur le vrai GitHub** (testé seulement avec une fausse API, le
   sandbox n'a pas accès à GitHub) : la bannière « sauvegarde plus récente »
   (elle n'apparaît qu'après une sauvegarde faite depuis un autre appareil).
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
