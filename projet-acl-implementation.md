# Mise en œuvre d'ACL IPv4 sur routeurs Cisco

![Cisco](https://img.shields.io/badge/Cisco%20IOS-1BA0D7?style=flat-square) ![ACL](https://img.shields.io/badge/ACL-1BA0D7?style=flat-square) ![Packet Tracer](https://img.shields.io/badge/Packet%20Tracer-1BA0D7?style=flat-square)

**Domaine :** Administration réseau / Sécurité réseau
**Environnement :** Cisco Packet Tracer — deux routeurs, plusieurs réseaux LAN et une connexion Internet simulée

## Contexte et objectif

Une ACL (Access Control List) filtre le trafic réseau selon des règles définies — adresses source/destination, protocole, port — appliquées à une interface dans une direction donnée. L'objectif de ce TP était de configurer quatre listes de contrôle d'accès distinctes (standard nommée, étendue numérotée, étendue nommée) sur une topologie à deux sites, afin de répondre à des exigences de sécurité précises tout en préservant la connectivité légitime entre les autres hôtes.

## Topologie et adressage

La maquette comprend deux routeurs interconnectés, chacun desservant plusieurs réseaux locaux, un lien vers un fournisseur Internet simulé, et plusieurs postes clients et serveurs (poste utilisateur interne, serveur web d'entreprise, poste et serveur d'un site distant, utilisateur et serveur web externes) répartis selon un plan d'adressage IPv4 défini à l'avance.

## Démarche

**1. Vérification de la connectivité initiale**
Avant toute configuration d'ACL, validation par ping que tous les hôtes du réseau peuvent se joindre mutuellement — un état de référence indispensable pour pouvoir ensuite attribuer sans ambiguïté tout blocage constaté à une règle ACL précise plutôt qu'à un problème de routage préexistant.

**2. ACL étendue numérotée — blocage FTP et ICMP depuis Internet**
Une première ACL étendue (ACL 101) bloque explicitement le trafic FTP entrant depuis Internet vers le serveur web d'entreprise, ainsi que tout trafic ICMP en provenance d'Internet vers le réseau local principal du site — tout en laissant passer le reste du trafic (HTTP notamment), conformément à l'exigence de ne pas bloquer plus que nécessaire.

**3. ACL étendue numérotée — isolation du serveur du site distant**
Une seconde ACL étendue (ACL 111) empêche les hôtes du réseau local principal d'accéder au serveur situé sur le site distant, sans affecter le reste du trafic inter-sites.

**4. ACL standard nommée — restriction d'accès aux lignes VTY**
Une ACL standard nommée (`vty_block`) restreint l'accès en administration à distance (Telnet/SSH) du routeur du site principal aux seules adresses d'un second réseau local interne désigné comme réseau d'administration — empêchant toute tentative de connexion aux lignes VTY depuis un autre segment.

**5. ACL étendue nommée — filtrage inter-sites**
Une ACL étendue nommée (`branch_to_hq`) contrôle le trafic provenant du site distant vers les réseaux locaux du site principal, tout en autorisant le reste du trafic IP à circuler normalement.

**6. Application et vérification**
Chaque ACL a été appliquée sur l'interface et dans la direction (entrante/sortante) les plus efficaces — au plus près de la source du trafic à filtrer, conformément aux bonnes pratiques Cisco — puis vérifiée avec `show ip access-lists` pour confirmer les compteurs de correspondance des règles.

## Résultat

La configuration complète (les 4 ACL, leur application sur les interfaces, et la topologie associée) est disponible dans le fichier Packet Tracer original, fourni ci-dessous pour vérification :

[📥 Télécharger le fichier Packet Tracer (.pkt)](TP-ACL-Implementation.pkt)

## Analyse

Ce TP illustre un principe central de la conception d'ACL : **filtrer au plus près de la source et dans la direction la plus efficace, sans bloquer plus que ce qu'exige chaque règle**. La consigne de ne jamais utiliser un `deny any` explicite en fin de liste rappelle qu'une ACL Cisco standard IOS applique déjà un refus implicite en fin de traitement — l'exprimer explicitement empêcherait toute exception ou évolution future sans réécrire la liste entière. La distinction entre ACL standard (filtrage sur adresse source uniquement, à placer près de la destination) et ACL étendue (filtrage sur source, destination, protocole et port, à placer près de la source) est le fil conducteur de la conception adoptée pour chacune des quatre règles.

## Compétences mobilisées

`Cisco IOS` `ACL standard et étendue (numérotée et nommée)` `Filtrage de trafic réseau` `Sécurisation de l'accès administratif (VTY)` `Cisco Packet Tracer`

---
[← Retour à l'accueil](index.md)
