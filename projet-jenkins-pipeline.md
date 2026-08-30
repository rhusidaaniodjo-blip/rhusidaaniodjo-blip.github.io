# Pipeline CI/CD avec Jenkins, Docker et tests automatisés

![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=flat-square) ![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square) ![Groovy](https://img.shields.io/badge/Groovy-4298B8?style=flat-square) ![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square)

**Domaine :** DevOps / Intégration continue
**Environnement :** Jenkins (local), Docker, Python 3.13 (Alpine), pipeline scripté en Groovy

## Contexte et objectif

L'objectif de ce projet était de construire un pipeline Jenkins complet orchestrant la conteneurisation, l'exécution et le nettoyage automatique d'une application de test : un script Python (`sum.py`) additionnant deux nombres passés en argument, validé contre un jeu de données de référence (`test_variable.txt`).

## Démarche

**1. Développement et débogage du script de test**
Le script `test_sum.py` lit un fichier de cas de test ligne par ligne et compare la sortie de `sum.py` à un résultat attendu. Une première exécution a révélé une erreur d'indentation Python, puis une erreur de nom de fichier (`test_variables.txt` au lieu de `test_variable.txt`) — deux erreurs classiques corrigées par itération directe sur le terminal.

<img width="1366" height="728" alt="image" src="https://github.com/user-attachments/assets/fd1a4a68-4830-42c3-aeb5-59e61de1a479" />


**2. Découverte d'un bug de comparaison de types**
Une fois les erreurs de script résolues, les tests s'exécutaient mais échouaient systématiquement : `sum.py` utilise `float()` pour convertir les arguments, produisant une sortie comme `3.0`, alors que le fichier de référence attend `3` (entier). La comparaison de chaînes de caractères entre `"3.0"` et `"3"` échoue même si le résultat mathématique est correct.

<img width="1366" height="728" alt="image" src="https://github.com/user-attachments/assets/2825ec00-b5b4-41e2-9c91-3a3d07f8bac3" />


**3. Conteneurisation de l'application testée**
Un `Dockerfile` basé sur `python:3.13.0-alpine3.20` embarque `sum.py` et maintient le conteneur actif (`tail -f /dev/null`) pour permettre l'exécution de commandes à l'intérieur via `docker exec`, plutôt que de lancer le script directement au démarrage du conteneur.

**4. Construction du pipeline Jenkins (Groovy)**
Le pipeline scripté définit plusieurs étapes : construction de l'image Docker, démarrage du conteneur en arrière-plan avec récupération de son ID, puis un stage "Run Tests" qui lit le fichier de cas de test et exécute `sum.py` dans le conteneur pour chaque ligne via `docker exec`.

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/d3d935c1-b3b3-4012-a3df-0a7047095767" />
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/c50561e8-ac9c-47bb-bfc6-63ddd00ce456" />
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/80720c4a-3093-46fd-9fea-4187a6e83603" />


**5. Nettoyage automatique post-exécution**
Un bloc `post { always { ... } }` arrête et supprime systématiquement le conteneur Docker à la fin du pipeline, que le build ait réussi ou échoué — garantissant qu'aucun conteneur orphelin ne reste actif après un test.

<img width="1366" height="768" alt="6" src="https://github.com/user-attachments/assets/63d6078a-a696-4246-b84e-87851b93c86e" />


## Résultat

Le pipeline s'exécute de bout en bout (build de l'image, lancement du conteneur, exécution des tests, nettoyage), mais les builds successifs (#13 à #15) échouent en raison du bug de comparaison de types identifié à l'étape 2 : la logique de test elle-même compare des chaînes de caractères sans normaliser leur format numérique.
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/72e20e20-b55c-4ddb-8e4a-114980e6bcbe" />



## Analyse

Ce projet illustre une réalité fréquente de l'intégration continue : **un pipeline qui s'exécute correctement n'est pas la même chose qu'un pipeline qui valide correctement**. L'infrastructure (build, exécution, nettoyage) fonctionnait comme prévu à chaque run, mais la logique de test contenait un défaut qui produisait des faux négatifs systématiques. Le correctif approprié serait de comparer les valeurs numériques converties (`float(output) == float(expected)`) plutôt que leurs représentations textuelles brutes — une leçon utile sur l'importance de valider la logique de test elle-même, pas seulement le code qu'elle est censée vérifier.

## Compétences mobilisées

`Jenkins` `Pipeline scripté (Groovy)` `Docker` `Python` `Débogage méthodique` `Intégration continue`

---
[← Retour à l'accueil](index.md)
