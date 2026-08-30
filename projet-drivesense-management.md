# DriveSense — Gestion de projet d'une application de livraison intelligente

![Gestion de projet](https://img.shields.io/badge/Gestion%20de%20projet-2E8B57?style=flat-square) ![Agile/Scrum](https://img.shields.io/badge/Agile%2FScrum-2E8B57?style=flat-square) ![Lean Canvas](https://img.shields.io/badge/Lean%20Canvas-2E8B57?style=flat-square) ![Trello](https://img.shields.io/badge/Trello-0079BF?style=flat-square)

**Domaine :** Gestion de projet / Méthodologie
**Outils :** Lean Canvas, Charte de projet, Trello, méthodologie Agile/Scrum

## Contexte et objectif

DriveSense est un projet de fin d'année visant à concevoir une application web/mobile d'optimisation de tournées de livraison en temps réel, destinée aux chauffeurs-livreurs professionnels. Plutôt qu'un projet purement technique, l'objectif ici était d'appliquer une méthodologie de gestion de projet complète — du cadrage initial jusqu'au suivi d'exécution — en combinant les techniques de gestion de projet classiques et agiles vues en cours.

## Démarche

**1. Cadrage et validation de l'idée (phase pré-engagement)**
Rédaction d'un résumé exécutif structurant le problème (inefficacité des tournées, perte de temps et de carburant), la solution envisagée (application d'optimisation GPS temps réel), l'objectif du projet et sa justification économique (réduction des coûts opérationnels, amélioration de la ponctualité). Cette phase s'est appuyée sur un Lean Canvas et une charte de projet formalisant le public cible (chauffeurs-livreurs professionnels) et l'hypothèse de valeur (optimisation GPS dynamique, priorisation visuelle des livraisons).

**2. Définition de la structure de découpage du projet (WBS)**
Le projet a été décomposé en huit lots de travail : gestion de projet, cadrage et spécifications, design UI/UX, développement backend (Node.js/Express, authentification JWT, PostgreSQL), intégration de l'API Google Maps, développement frontend (tracking GPS temps réel), tests et validation, puis déploiement.

**3. Planification par jalons**
Sept jalons ont structuré l'avancement du projet, de la validation du Lean Canvas (M1) jusqu'au MVP prêt pour les tests (M7), en passant par la validation du prototype UI, la mise en place de la base de données, le backend fonctionnel, l'intégration Google Maps et le tracking GPS temps réel.

**4. Suivi d'exécution avec Trello (méthode Agile/Scrum)**
Le suivi opérationnel du projet a été organisé en sprints mensuels sur un tableau Trello : Sprint 1 (mise en place du projet — lancement, spécifications, planning), Sprint 2 (prototype et données — API, prototype v1, interface utilisateur), Sprint 3 (optimisation et IA — algorithme d'optimisation, tests), et Sprint 4 (finalisation et présentation — finalisation du prototype, soutenance). Chaque carte est assignée à un membre de l'équipe avec suivi de progression par sous-tâches.

**5. Identification et gestion des risques**
Trois risques majeurs ont été identifiés en amont avec leur stratégie d'atténuation associée : un risque technique (l'API Google Maps pourrait ne pas optimiser correctement les routes sous forte charge, atténué par des tests de montée en charge anticipés et un script de secours local), un risque de périmètre (ajout de fonctionnalités non planifiées, atténué par le respect strict du backlog initial), et un risque ressources (indisponibilité d'un membre clé de l'équipe, atténué par la documentation cross-équipe et le pair programming).

**6. Exécution et contrôle continu**
Mise en place de rituels de suivi (daily stand-up de 15 minutes, revues de sprint), d'un contrôle qualité portant sur la validation des données et des priorités visuelles, et d'un processus formalisé de gestion des changements évaluant systématiquement l'impact de toute modification sur le coût, le délai et le périmètre du projet.

## Résultat

Le projet a produit l'ensemble des livrables de cadrage attendus (résumé exécutif, Lean Canvas, charte, WBS, échéancier par jalons) ainsi qu'un tableau de suivi Trello structuré en quatre sprints, avec attribution des tâches par membre d'équipe et suivi de la progression par sous-tâches.

## Analyse

Ce projet illustre une approche hybride de gestion de projet, combinant une phase amont structurée façon cycle en V (cadrage, spécifications, validation par jalons) avec une exécution itérative en sprints façon Agile — un choix pertinent pour un projet étudiant à durée fixe où la vision produit doit être validée tôt, tandis que le développement bénéficie de la flexibilité et de la visibilité offertes par des sprints courts. L'identification proactive des risques (notamment la dépendance à une API tierce sous forte charge) démontre une anticipation des points de fragilité technique dès la phase de cadrage, plutôt que de les découvrir en cours de développement.

## Compétences mobilisées

`Gestion de projet` `Lean Canvas` `Méthodologie Agile/Scrum` `Trello` `Gestion des risques` `Structure de découpage de projet (WBS)` `Planification par jalons`

---
[← Retour à l'accueil](index.md)
