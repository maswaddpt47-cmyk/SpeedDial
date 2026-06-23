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
