# Conteneurisation et intégration continue d'une application Flask

![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square) ![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?style=flat-square) ![Flask](https://img.shields.io/badge/Flask-000000?style=flat-square) ![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square)

**Domaine :** DevOps / Intégration continue
**Environnement :** Ubuntu, Docker, GitHub Actions, Python 3.12 / Flask

## Contexte et objectif

L'objectif de ce projet était de prendre en main le cycle complet de mise en production d'une application web : développement local, tests automatisés, conteneurisation avec Docker, versionning Git, puis mise en place d'un pipeline d'intégration continue (CI) qui exécute automatiquement les tests à chaque push sur GitHub.

## Démarche

**1. Développement de l'application et des tests**
J'ai créé une application Flask minimale exposant une route `/` retournant "Hello, World!", accompagnée d'un test unitaire avec `pytest` vérifiant le code de statut HTTP et le contenu de la réponse.

**2. Mise en place de l'environnement local**
Configuration d'un environnement virtuel Python (`venv`) et installation des dépendances (`Flask`, `pytest`) via `pip install -r requirements.txt`, puis validation manuelle de l'application en local (`python app.py`) avant toute conteneurisation.

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/d7d9cbf7-4663-428b-a768-530b04f3a4e6" />



**3. Conteneurisation avec Docker**
Rédaction d'un `Dockerfile` basé sur `python:3.12-slim`, installant les dépendances et exposant le port 5000. Le build (`docker build`) et l'exécution du conteneur (`docker run -p 5000:5000 flask-app`) ont permis de valider que l'application fonctionne de manière identique en dehors de l'environnement de développement local — l'un des principaux intérêts de la conteneurisation.

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/20b5ab39-4aae-44a8-86b2-892a080f8615" />

**4. Versionning et résolution d'un problème d'authentification Git**
Après un premier `git commit` incluant par erreur le dossier `venv/` (plus de 1500 fichiers), j'ai initialisé le dépôt distant sur GitHub. La tentative de push en HTTPS a échoué : GitHub ne supporte plus l'authentification par mot de passe pour les opérations Git. J'ai résolu ce point en générant une paire de clés SSH (`ssh-keygen`), en l'ajoutant à l'agent SSH, puis en basculant l'URL du remote de HTTPS vers SSH (`git remote set-url origin git@github.com:...`) — un push authentifié a alors réussi.

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/b928ed2c-de85-465c-a329-ff1254ae4e2a" />
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/aa08ce18-b102-470a-93ca-bc593b663789" />
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/ba94aef2-5b7f-4ff1-9a54-4d294aaaddcd" />

**5. Mise en place du pipeline CI avec GitHub Actions**
Création du fichier `.github/workflows/ci.yml` définissant un pipeline qui se déclenche à chaque push ou pull request sur `main` : installation de Python 3.12, installation des dépendances, puis exécution automatique de la suite de tests avec `pytest`.

```yaml
name: Flask CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout repo
      uses: actions/checkout@v3
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.12'
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Run tests
      run: |
        pytest
```
<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/005778c4-f2ef-4f1d-bbbd-f7b73477f304" />

## Résultat

L'application est fonctionnelle en local, en conteneur Docker, et versionnée sur GitHub avec un pipeline CI qui valide automatiquement chaque changement via les tests unitaires.

## Analyse

Ce projet couvre un cycle DevOps de bout en bout à petite échelle : le Dockerfile garantit que l'environnement d'exécution est reproductible indépendamment de la machine, tandis que le pipeline CI garantit qu'aucune régression ne peut être fusionnée sans passer les tests. Le blocage d'authentification Git rencontré en cours de route est un cas très représentatif de la réalité du terrain (GitHub a désactivé l'authentification par mot de passe en 2021) — la clé SSH générée pour l'occasion est restée strictement locale au projet, illustrant une bonne pratique de sécurité : ne jamais réutiliser une clé de test dans un contexte de production.

**Point d'amélioration identifié :** committer le dossier `venv/` était une erreur qui pourrait être évitée avec un `.gitignore` approprié — leçon retenue pour les projets suivants.

## Compétences mobilisées

`Docker` `GitHub Actions (CI)` `Flask` `pytest` `Git / SSH` `Intégration continue`

---
[← Retour à l'accueil](index.md)
