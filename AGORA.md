# AGORA — SpeedDial

Débats soumis à un **contradicteur** : une autre session Claude, ou Codex
(OpenAI) pour la sécurité, l'authentification, les données personnelles et
tout calcul affiché. Une session dépose ici une proposition ; le contradicteur,
sans le même contexte, lit les vrais fichiers et répond. L'utilisateur tranche.
Quand soumettre : section « AGORA » de `CLAUDE.md`. `MD-LIB/agora.md` n'est
pas requis pour ouvrir un bloc ni pour y répondre.

## Pour répondre à un bloc

- **Jamais un bloc que l'on a soi-même ouvert.** Avant de répondre, comparer
  le trailer `Claude-Session:` du commit qui a déposé le bloc
  (`git log -1 --format=%B <sha du bloc>`) à celui de la session courante :
  identiques, on ne répond pas. Le champ `Auteur` n'est qu'un libellé.
  Trailer absent (commit fait à la main) : demander à l'utilisateur.
- **Append-only** : on ajoute son bloc, on n'édite jamais celui d'un autre.
  `git pull --rebase origin main` juste avant de pousser, et push **direct
  sur `main`** (règle du projet) : deux sessions sur deux branches ne se
  voient pas.
- **Aucune donnée d'usager** dans un bloc : ni raccourci réel, ni token, ni
  extrait de sauvegarde.

**Le cycle** : une session dépose un bloc et le pousse sur `main`, puis
l'annonce à l'utilisateur ; l'utilisateur dit « pull » à Codex (section
« AGORA — ce que « pull » veut dire » de `AGENTS.md`) ou colle la phrase de
relais dans une autre session Claude : « pull **maswaddpt47-cmyk/SpeedDial**,
lis son `AGORA.md`, réponds à AG-00N, tu es la session B » ; le contradicteur
répond ; l'utilisateur tranche. **Aucune notification ne passe d'un outil ou
d'un compte à l'autre** : le relais par l'utilisateur est obligatoire, c'est
pourquoi l'AGORA ne bloque jamais le travail.

## Sincérité

1. **« Amendé » n'est valable que s'il nomme ce qui serait faux, manquant ou
   coûteux** si la proposition était appliquée telle quelle. Un amendement qui
   ne change ni code, ni décision, ni chiffre est un « confirmé ».
2. **« Confirmé » est une réponse pleine**, pas un aveu d'inutilité. Ne jamais
   chercher un amendement pour justifier sa présence.
3. **Aucune appréciation** de la proposition ni de son auteur : une réponse
   commence par un constat.

Le tableau des blocs tranchés porte le verdict et le total des trois issues.
**Le total est une alerte, pas un objectif** : le verdict découle de la
contrainte 1 appliquée au bloc, jamais de la série.

## Gabarit

```markdown
## AG-00N — Titre court — ouvert le JJ/MM/AAAA
**Auteur** : session <8 car. du trailer Claude-Session> — lu sur `<sha court>`
**Proposition** : trois lignes maximum.
**Critère déclencheur** : n° et lequel (section « AGORA » de `CLAUDE.md`).
**Contradicteur** : Codex | session B
**Ce que ça engage** : ce qui serait coûteux à défaire.
**Non vérifié par l'auteur** : le champ le plus important — dire où l'on est
faible oriente le contradicteur.
**Si personne ne répond, je fais quoi ?** — si c'est « je continue pareil », le
bloc n'avait pas lieu d'être.
**Où regarder** : fichier:ligne

### Réponse — JJ/MM/AAAA
**Auteur** : session <autre id> ou Codex — lu sur `<sha court>`
**Verdict** : confirmé | amendé | contredit
**Constat** : avec fichier:ligne, mesure ou log — sans preuve, la réponse ne compte pas.
**Amendement** : ...

### Tranché le JJ/MM/AAAA — décision : ...
```

Un bloc tranché **sort** de la section des blocs ouverts : sa conclusion
remonte dans `CHANTIERS.md` (points à ne pas défaire) et une ligne reste
ci-dessous ; le récit est dans `git log`.

# Blocs ouverts

Aucun.

# Blocs tranchés

Aucun. Total : 0 confirmé, 0 amendé, 0 contredit.
