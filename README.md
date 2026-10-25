# safe-refactoring-migration

Guide Claude pour **refactorer, migrer ou mettre à niveau** du code sans le casser. Refactorer, c'est changer la structure sans changer le comportement ; le danger est de ne pas savoir qu'on l'a changé tant qu'on ne l'a pas mesuré.

Skill Claude : un dossier d'instructions que Claude charge automatiquement quand ta demande correspond. Tu n'as rien à invoquer : décris ton problème normalement.

## Quand il se déclenche

- un refactoring, un « nettoyage » de code, une réécriture ;
- une montée de version de framework ou de langage, un changement de bibliothèque ;
- une migration de schéma de base de données ;
- une demande du type « migrer vers », « upgrade », « passer de X à Y », même sans parler de risque.

Exemples de messages :

- « Je veux migrer mon projet de Express 4 vers Express 5. »
- « Peux-tu nettoyer et restructurer ce module de 800 lignes ? »
- « Comment renommer cette colonne en production sans interruption ? »

## Ce que le skill fait faire à Claude

1. **Définir le périmètre** : ce qui change, pourquoi, et ce qui **ne doit pas** changer (API publique, formats de données, comportements observables). Pour une migration : versions source et cible, notes de migration officielles (recherchées sur le web pour les versions récentes).
2. **Cartographier l'existant** : usages, dépendances, points d'entrée, zones sans tests, comportements implicites.
3. **Poser un filet de sécurité** : lancer les tests existants *avant* de toucher au code (pour distinguer un test déjà rouge d'une régression), écrire des **tests de caractérisation** si la couverture est faible, sauvegarder et tester sur une copie avant une migration de base.
4. **Avancer par petits pas** : un changement, un objectif, un commit, les tests après chaque pas. Techniques : branch by abstraction, strangler, expand / migrate / contract pour les schémas.
5. **Prévoir le retour arrière** à chaque étape (`git revert`, migration inverse, feature flag, sauvegarde). Les étapes irréversibles passent en dernier et exigent une confirmation explicite.
6. **Vérifier et nettoyer** : suite de tests complète, code mort supprimé, documentation à jour.

Pièges signalés : sauter plusieurs versions majeures, verrouiller une grande table en production, mélanger renommage et changement de logique, supprimer du code appelé dynamiquement.

## Format de la réponse

Un **plan** présenté avant tout changement important :

```
## Objectif et contraintes
## État de référence (tests actuels, couverture)
## Étapes (action, vérification, retour arrière)
## Risques et étapes irréversibles
```

Claude demande ta validation avant toute étape irréversible.

## Installation

**Claude Code**

```bash
git clone https://github.com/AGBERESahir/safe-refactoring-migration.git ~/.claude/skills/safe-refactoring-migration
```

Le dossier doit s'appeler `safe-refactoring-migration` et contenir `SKILL.md` à sa racine. Relance Claude Code si une session était ouverte.

**Claude.ai**

Zippe le dossier `safe-refactoring-migration` (avec `SKILL.md` dedans), puis ajoute l'archive dans **Paramètres > Skills**.

## Vérifier qu'il fonctionne

Envoie l'un des messages d'exemple ci-dessus. La réponse doit suivre la structure décrite plus haut. Si Claude répond de façon libre, vérifie que le skill est activé dans tes paramètres et que `SKILL.md` n'est pas dans un sous-dossier.

## Limites

- Le plan n'est fiable que si Claude voit le code concerné et ses tests : donne-lui le périmètre exact.
- Les notes de migration changent avec les versions : le skill demande de les chercher plutôt que de se fier à la mémoire, mais vérifie toujours la version cible dans la documentation officielle.
- Une migration de base de données en production doit toujours être répétée sur une copie, avec une sauvegarde **vérifiée**, avant d'être lancée.
- Le skill n'exécute rien tout seul : tu gardes la main sur chaque étape.

## Voir aussi

- [systematic-debugging](https://github.com/AGBERESahir/systematic-debugging) : si un pas du refactoring casse quelque chose.
- [architecture-data-review](https://github.com/AGBERESahir/architecture-data-review) : pour concevoir le schéma cible d'une migration.
- [code-security-review](https://github.com/AGBERESahir/code-security-review) : relire le résultat avant de le fusionner.

## Fichiers

| Fichier | Rôle |
|---|---|
| `SKILL.md` | Les instructions : nom, description de déclenchement, démarche et format de sortie. |
| `README.md` | Cette page, pour les humains (Claude ne la lit pas). |
| `LICENSE` | Licence MIT. |

## Licence

MIT, voir [LICENSE](LICENSE).
