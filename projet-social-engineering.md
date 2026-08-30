# Simulation de test d'ingénierie sociale (OSINT & Phishing)
![Kali Linux](https://img.shields.io/badge/Kali%20Linux-557C94?style=flat-square) ![ARP Spoofing](https://img.shields.io/badge/ARP%20Spoofing-D83B01?style=flat-square) ![Wireshark](https://img.shields.io/badge/Wireshark-1679A7?style=flat-square)

**Domaine :** Sécurité offensive / Ingénierie sociale
**Environnement :** Lab personnel — Kali Linux, Social-Engineer Toolkit (SET)
**Cible :** Entreprise fictive simulée à des fins pédagogiques

## Contexte et objectif

L'ingénierie sociale exploite la confiance humaine plutôt que des failles techniques — c'est souvent le vecteur d'attaque le plus efficace, car il contourne des défenses techniques par ailleurs solides. L'objectif de ce projet était de reproduire, sur une entreprise fictive, la méthodologie complète d'un test d'ingénierie sociale légal : de la reconnaissance d'informations publiques jusqu'à la simulation d'une campagne de phishing, pour comprendre comment un attaquant construit un scénario crédible — et donc comment s'en défendre.

## Démarche

**1. Reconnaissance et collecte d'informations (OSINT)**
J'ai simulé la phase de renseignement en identifiant les informations qu'un attaquant collecterait légitimement sur une cible : nom de domaine, format des adresses email, noms et fonctions des employés clés, présence sur les réseaux professionnels (LinkedIn) et sociaux. Cette étape illustre à quel point des informations apparemment anodines, publiées volontairement par une organisation ou ses employés, peuvent former la base d'une attaque ciblée.

**2. Automatisation de la collecte avec des outils OSINT**
J'ai utilisé `theHarvester` pour illustrer comment automatiser la recherche d'adresses email et de sous-domaines associés à un nom de domaine, et `metagoofil` pour montrer comment des documents publics (PDF, DOC) peuvent révéler des métadonnées exploitables — noms d'auteurs, logiciels utilisés, dates internes.

**3. Vérification d'exposition de données**
Vérification de la présence d'adresses email dans des bases de fuites de données connues (Have I Been Pwned), pour évaluer si des identifiants pourraient déjà être compromis et réutilisables dans une attaque.

**4. Simulation d'une campagne de phishing avec le Social-Engineer Toolkit**
À l'aide du Social-Engineer Toolkit (SET), j'ai exploré le module d'attaques web, qui propose plusieurs vecteurs (clonage de site avec récupération d'identifiants, tabnabbing, attaques combinées). J'ai configuré un scénario de clonage de site pour illustrer comment une page de connexion falsifiée peut être utilisée pour intercepter des identifiants saisis par une victime.

<img width="755" height="566" alt="image" src="https://github.com/user-attachments/assets/ff45f8fb-5b5f-4ad2-a4cb-0a18be510271" />
<img width="755" height="566" alt="image" src="https://github.com/user-attachments/assets/a2c984c9-44fd-43fb-88ff-57809ff5efa3" />
<img width="755" height="566" alt="image" src="https://github.com/user-attachments/assets/ce25c7a5-45f8-4a33-b971-56c7214b32b6" />
<img width="755" height="566" alt="image" src="https://github.com/user-attachments/assets/aee00c08-7836-41ac-a100-55d7a7087c84" />
<img width="755" height="566" alt="image" src="https://github.com/user-attachments/assets/08d57bf3-90f1-48ed-ad4c-362be1f8e87a" />
<img width="755" height="566" alt="image" src="https://github.com/user-attachments/assets/94b30f14-5661-41ea-90f7-83d9e1bce62e" />
<img width="755" height="566" alt="image" src="https://github.com/user-attachments/assets/fc308b94-a784-497b-b0f2-8348f531e777" />
<img width="755" height="566" alt="image" src="https://github.com/user-attachments/assets/d2440a17-3921-4742-948a-a9038a511e2e" />
<img width="755" height="566" alt="image" src="https://github.com/user-attachments/assets/d0dbd617-f149-4ef4-8b28-6412bb15fb12" />
<img width="755" height="566" alt="image" src="https://github.com/user-attachments/assets/ad33cdc6-2800-4123-b0b0-6a67cb1238c1" />


## Résultat

L'exercice a permis de reconstituer, étape par étape, le déroulement complet d'une attaque de social engineering ciblée — de la reconnaissance passive à la mise en place technique d'un piège de phishing — sans qu'aucune action ne soit dirigée contre une cible réelle.

## Analyse et remédiation

Ce projet met en évidence que la sécurité d'une organisation ne se limite pas à ses défenses techniques : **l'information publiée volontairement (réseaux sociaux, métadonnées de documents, organigrammes) constitue une surface d'attaque à part entière**. Les axes de remédiation identifiés :

- **Sensibilisation des employés** : formation régulière à la reconnaissance des tentatives de phishing et à la prudence sur les informations partagées publiquement.
- **Hygiène des documents publics** : nettoyage systématique des métadonnées avant publication de documents externes.
- **Surveillance des fuites de données** : vérification proactive de l'exposition des adresses professionnelles dans des bases de données compromises.
- **Authentification renforcée** : la mise en place de l'authentification à plusieurs facteurs limite fortement l'impact d'un identifiant compromis par phishing.

## Compétences mobilisées

`OSINT` `Social-Engineer Toolkit (SET)` `theHarvester` `metagoofil` `Méthodologie de test d'intrusion` `Sensibilisation à la sécurité`

---
[← Retour à l'accueil](index.md)
