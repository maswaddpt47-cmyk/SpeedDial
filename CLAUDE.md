# Règles de travail — SpeedDial

## Avant toute intervention

1. `git pull origin main` — obligatoire, sans exception, avant de lire ou modifier le moindre fichier, même si le repo semble à jour.
2. Vérifier `git status` et la cohérence entre CLAUDE.md et les instructions de session avant d'agir — signaler tout conflit avant, pas après.
3. Backup daté de chaque fichier modifié dans `.backups/` (gitignored) :
   - Format : `.backups/<fichier>.<YYYYMMDD-HHMM>`
   - Exemple : `.backups/index.html.20260623-1430`

## Commits

- Un commit par modification distincte.
- Préfixe obligatoire : `feat:`, `fix:`, ou `refactor:`.
- Message clair décrivant ce qui change et pourquoi.

## Push

- Toujours pousser directement sur `main` : `git push origin main`
- Jamais sur une branche intermédiaire sauf instruction explicite de session.

---

## Règles de collaboration — Côté Claude

### Priorité haute

1. Ne jamais présenter une explication technique plausible comme un fait : marquer explicitement "hypothèse non vérifiée" tant qu'aucune preuve (log, capture, test réel) ne la confirme.
1bis. Utiliser des dates explicites (JJ/MM ou JJ/MM/AAAA) plutôt que des termes relatifs ("hier", "aujourd'hui", "demain", "la semaine dernière") — la perception du temps vient d'un contexte injecté en début de session, pas d'une horloge en temps réel, et devient peu fiable sur une session qui s'étale sur plusieurs jours ou reprises.
2. Ne jamais déclarer "c'est réparé", "c'est en ligne" ou "testé" sans vérification réelle du chemin critique (déploiement, rendu navigateur, test exécuté) — pas une lecture de code qui "devrait marcher".
3. Sur toute demande d'audit ou de correction d'un bug, livrer un audit systématique (tous les points d'impact) avant la première correction.
4. Signaler explicitement toute déviation d'une spec ou toute décision de design prise seul, au moment où elle est prise — jamais en note après coup.
5. Poser une question de clarification dès qu'une demande est réellement ambiguë (contenu non précisé, référence visuelle absente) plutôt que de trancher en silence.
6. Après toute reprise de session ou résumé de contexte, relire l'état réel du fichier concerné avant de le modifier — ne jamais présumer qu'un correctif précédent est encore en place.
7. Avant de pousser un changement visuel (CSS/layout), vérifier mentalement les interactions à risque : stacking context, overflow, `position: sticky/fixed`, `transform` → nouveau stacking context.

### Bonnes pratiques à maintenir

8. Demander confirmation avant toute action à fort impact (déploiement, architecture, migration de données) et exécuter vite dès validation courte reçue.
9. Privilégier la preuve concrète (logs, captures, Network DevTools, console) sur la déduction théorique pour tout diagnostic.

---

## Règles de collaboration — Côté utilisateur

### Priorité haute

1. Donner le contexte temporel et les tentatives déjà faites dès le premier message ("ça marchait hier", "j'ai déjà testé X") plutôt qu'après coup.
2. Pour un bug visuel ou "bizarre", ajouter une description du symptôme précis ou une capture annotée plutôt qu'une formule vague.
3. Signaler en début de message tout changement d'état fait hors session (redéploiement, branche renommée, settings modifiés).
4. Pour les demandes ouvertes ("plus", "mieux", "améliore"), préciser le critère de succès attendu.
5. Donner un retour de validation réelle après test terrain, même court ("testé, ça marche" / "ça casse en fait").

### Bonnes pratiques à maintenir

6. Continuer à valider court et vite sur le travail bien cadré — ça fonctionne bien tant que la portée est claire.
7. Continuer à recadrer immédiatement dès qu'une mauvaise direction est repérée.
