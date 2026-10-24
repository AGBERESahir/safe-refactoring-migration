---
name: safe-refactoring-migration
description: Démarche pour refactorer, migrer ou mettre à niveau du code sans le casser - tests de caractérisation d'abord, petits pas vérifiables, plan de retour arrière. À utiliser pour un refactoring, une montée de version de framework ou de langage, un changement de bibliothèque, une migration de schéma de base de données, une réécriture, "nettoyer ce code", "migrer vers", "upgrade", "passer de X à Y", même si l'utilisateur ne parle pas de risque. Use for refactoring, framework upgrades, dependency migrations and schema changes.
---

# Refactoring et migration sûrs

Refactorer, c'est changer la structure sans changer le comportement. Le danger : on ne sait pas qu'on a changé le comportement tant qu'on ne l'a pas mesuré. La démarche consiste donc à figer le comportement actuel, avancer par petits pas et pouvoir revenir en arrière à tout moment.

## Démarche

### 1. Définir le périmètre et l'objectif
Quoi change, pourquoi, et ce qui **ne doit pas** changer (API publique, formats de données, comportements observables). Pour une migration : versions source et cible, et lecture des notes de migration/changelog officiels (rechercher sur le web pour les versions récentes plutôt que de se fier à la mémoire).

### 2. Cartographier l'existant
Trouver les usages (recherche dans le code), les dépendances, les points d'entrée et les zones sans tests. Estimer le rayon d'impact. Signaler les comportements implicites (effets de bord, ordre d'exécution, valeurs par défaut).

### 3. Poser un filet de sécurité
- Si des tests existent : les lancer **avant** de toucher quoi que ce soit pour connaître l'état de référence (des tests déjà rouges doivent être notés, pas confondus avec des régressions).
- Si la couverture est faible : écrire des **tests de caractérisation** qui capturent le comportement actuel, même s'il est discutable. Ils décrivent ce que fait le code, pas ce qu'il devrait faire.
- Pour une base de données : sauvegarde vérifiée et copie de test avant toute migration.

### 4. Avancer par petits pas
Un changement = un objectif = un commit. Après chaque pas, lancer les tests. Si un pas échoue, il est petit donc facile à diagnostiquer ou annuler. Ne jamais mélanger refactoring et ajout de fonctionnalité dans le même pas.

Techniques utiles :
- **Branch by abstraction** : introduire une interface, migrer les appelants un par un, puis supprimer l'ancien code
- **Strangler** : faire cohabiter ancien et nouveau, basculer progressivement
- **Expand / migrate / contract** pour les schémas : ajouter la nouvelle colonne, écrire dans les deux, migrer les données, basculer la lecture, supprimer l'ancienne colonne en dernier

### 5. Prévoir le retour arrière
Pour chaque étape, savoir comment annuler : `git revert`, migration inverse, interrupteur de fonctionnalité (feature flag), sauvegarde. Les étapes **irréversibles** (suppression de colonne, perte de données) vont en dernier et exigent une confirmation explicite.

### 6. Vérifier et nettoyer
Relancer toute la suite de tests, vérifier les performances si pertinent, supprimer le code mort et les couches temporaires, mettre à jour la documentation.

## Pièges fréquents
- Montée de version en sautant plusieurs versions majeures : passer version par version
- Migration de schéma verrouillant une grande table en production : préférer des opérations non bloquantes, par lots
- Renommer et changer la logique dans le même commit
- Supprimer du code "inutilisé" appelé dynamiquement (réflexion, noms de chaînes, configuration)

## Format du plan à présenter
```
## Objectif et contraintes
## État de référence (tests actuels, couverture)
## Étapes (numérotées, chacune : action, vérification, retour arrière)
## Risques et étapes irréversibles
```
Présenter le plan **avant** d'exécuter les changements importants, et demander validation quand une étape est irréversible.
