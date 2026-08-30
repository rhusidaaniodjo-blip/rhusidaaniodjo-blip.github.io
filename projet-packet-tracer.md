# Segmentation réseau par VLAN sur commutateur Cisco
![Kali Linux](https://img.shields.io/badge/Kali%20Linux-557C94?style=flat-square) ![ARP Spoofing](https://img.shields.io/badge/ARP%20Spoofing-D83B01?style=flat-square) ![Wireshark](https://img.shields.io/badge/Wireshark-1679A7?style=flat-square)

**Domaine :** Administration réseau
**Environnement :** Cisco Packet Tracer — commutateur gamme 2950

## Contexte et objectif

Un VLAN (Virtual LAN) permet de segmenter un réseau physique en plusieurs domaines de diffusion (broadcast) logiquement isolés, sans avoir besoin de câblage ou d'équipement supplémentaire. L'objectif de ce projet était de configurer un commutateur de zéro, créer deux VLAN distincts, y répartir des ports, puis vérifier concrètement l'effet de cette segmentation sur la connectivité entre machines.

## Démarche

**1. Configuration de base et sécurisation du commutateur**
J'ai configuré le commutateur (nom d'hôte `Switch_A`) avec un mot de passe enable secret, des mots de passe console/VTY, et une adresse IP de gestion sur le VLAN 1 par défaut — les fondations indispensables avant toute configuration avancée.

<img width="754" height="363" alt="image" src="https://github.com/user-attachments/assets/aac779e4-867f-4803-89a6-2ed9984b2a5f" />


**2. Validation de la connectivité initiale**
Deux PC ont été configurés dans le même sous-réseau et connectés à des ports appartenant tous deux au VLAN 1 par défaut. Le ping entre les deux a réussi, confirmant un état de référence fonctionnel avant toute segmentation.

<img width="755" height="545" alt="image" src="https://github.com/user-attachments/assets/ff088b19-62b9-4d52-8fc8-18c2b4756ed5" />
<img width="755" height="424" alt="image" src="https://github.com/user-attachments/assets/54fb29ae-1745-4d79-abb6-bd4e30580aa7" />
<img width="755" height="424" alt="image" src="https://github.com/user-attachments/assets/9ce344f0-e935-4b13-a698-9bad61772ed7" />


**3. Création et affectation des VLAN**
J'ai créé deux VLAN nommés (VLAN 2 et VLAN 3), puis affecté plusieurs ports de chacun à un VLAN spécifique via les commandes `switchport mode access` et `switchport access vlan`.

<img width="966" height="543" alt="image" src="https://github.com/user-attachments/assets/79bc5282-d051-4e63-934f-3308660f5319" />
<img width="965" height="341" alt="image" src="https://github.com/user-attachments/assets/34deeb96-a5db-4357-9dcd-452767f6c1a9" />


**4. Tests de connectivité croisés**
J'ai testé la connectivité entre machines dans différentes combinaisons de VLAN pour observer concrètement l'effet de la segmentation :

| Test | VLAN respectifs | Résultat |
|---|---|---|
| PC A ↔ PC B (ports initiaux) | VLAN 1 / VLAN 2 | Échec |
| PC B → Switch | VLAN 2 (switch en VLAN 1) | Échec |
| PC A ↔ PC B (après transfert dans le même VLAN) | VLAN 2 / VLAN 2 | Réussite |
| PC A ↔ PC B (VLAN différents) | VLAN 2 / VLAN 3 | Échec |

<img width="851" height="327" alt="image" src="https://github.com/user-attachments/assets/d2d6aedb-7797-4c26-98b3-714170136d85" />
<img width="967" height="543" alt="image" src="https://github.com/user-attachments/assets/75204ff8-5657-46ab-b218-0c43868c89f2" />
<img width="700" height="384" alt="image" src="https://github.com/user-attachments/assets/87f4a0d8-89ba-483f-b527-4ce134391e0c" />
<img width="666" height="288" alt="image" src="https://github.com/user-attachments/assets/a7da667a-4c80-4eff-8893-435dc0cd0187" />
<img width="763" height="287" alt="image" src="https://github.com/user-attachments/assets/49db79c4-c05b-4257-97aa-c58b90b747e6" />
<img width="655" height="309" alt="image" src="https://github.com/user-attachments/assets/abd25a5e-bed9-4049-afad-ca86b75a3d2b" />


## Résultat

La segmentation VLAN a fonctionné comme attendu : deux machines ne peuvent communiquer que si elles appartiennent au même VLAN, indépendamment de leur appartenance au même sous-réseau IP physique. Le transfert d'un port d'un VLAN à un autre suffit à changer instantanément le comportement réseau d'une machine.

## Analyse

Ce projet illustre un principe clé de conception réseau : **la segmentation logique (VLAN) est indépendante du câblage physique**. Deux machines connectées au même commutateur physique peuvent être totalement isolées l'une de l'autre si elles appartiennent à des VLAN différents — un mécanisme essentiel pour cloisonner des services, des départements, ou des niveaux de confiance sur une même infrastructure, sans multiplier les équipements physiques.

Point notable observé pendant les tests : le commutateur lui-même n'est joignable que depuis un VLAN correspondant à son IP de gestion (VLAN 1 par défaut) — un détail à anticiper en administration réelle pour ne pas se couper l'accès de gestion après une reconfiguration.

## Compétences mobilisées

`Cisco IOS` `Configuration de VLAN` `Segmentation réseau` `Cisco Packet Tracer` `Diagnostic de connectivité (ping)`

---
[← Retour à l'accueil](index.md)
