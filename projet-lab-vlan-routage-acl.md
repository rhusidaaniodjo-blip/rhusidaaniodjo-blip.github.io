# Lab réseau : VLAN, routage inter-VLAN et ACL

![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer-1BA0D7?logo=cisco&logoColor=white)
![VLAN](https://img.shields.io/badge/VLAN-802.1Q-0A66C2)
![Routage](https://img.shields.io/badge/Routage-router--on--a--stick-2E7D32)
![ACL](https://img.shields.io/badge/S%C3%A9curit%C3%A9-ACL%20%C3%A9tendues-C62828)

## Objectif

Segmenter un petit réseau d'entreprise en deux services isolés (Compta et IT), les faire communiquer à travers un routeur, puis filtrer le trafic avec une ACL pour interdire à la Compta de joindre l'IT.

## Topologie

| Équipement | Rôle | Adressage |
|---|---|---|
| PC0 | Poste Compta (VLAN 10) | 192.168.10.10/24, passerelle 192.168.10.1 |
| PC1 | Poste IT (VLAN 20) | 192.168.20.10/24, passerelle 192.168.20.1 |
| Switch 2960 | `Fa0/1` en VLAN 10, `Fa0/2` en VLAN 20, `Gi0/1` en trunk | n/a |
| Routeur 2911 | `Gi0/0.10` et `Gi0/0.20` (sous-interfaces dot1Q) | 192.168.10.1 et 192.168.20.1 |

> <img width="1080" height="1350" alt="slide-07" src="https://github.com/user-attachments/assets/6f728b98-eaf2-4cd7-b81f-82e1b2360ab9" />


## Démarche

### 1. Réseau de base et test de connectivité
Deux PC reliés à un switch par des câbles droits, adressage statique `192.168.1.10` et `.11`. Le `ping` réussit (4 réponses sur 4, `TTL=128`) : liaison et adressage validés.


### 2. Segmentation avec des VLAN
Création des VLAN 10 (COMPTA) et 20 (IT), puis affectation des ports en mode `access`. Un `show vlan brief` confirme `Fa0/1` dans COMPTA et `Fa0/2` dans IT.

Test d'isolation : le `ping` entre les deux PC échoue (100 % de perte), alors qu'ils sont sur le même switch. Le VLAN joue bien son rôle de cloison.

```
vlan 10
 name COMPTA
vlan 20
 name IT
interface fastEthernet 0/1
 switchport mode access
 switchport access vlan 10
interface fastEthernet 0/2
 switchport mode access
 switchport access vlan 20
```


### 3. Routage inter-VLAN (router-on-a-stick)
Chaque VLAN reçoit son propre réseau. Le lien switch-routeur est configuré en **trunk** pour transporter les deux VLAN, et le routeur utilise une **sous-interface par VLAN** comme passerelle.

```
! Switch
interface gigabitEthernet 0/1
 switchport mode trunk

! Routeur
interface gigabitEthernet 0/0
 no shutdown
interface gigabitEthernet 0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
interface gigabitEthernet 0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
```

Vérifications :
- `show ip interface brief` : les deux sous-interfaces sont `up/up`.
- `ping 192.168.20.10` depuis PC0 : réponses reçues avec `TTL=127` (un routeur traversé). La première requête expire, le temps de la résolution ARP.
- `tracert 192.168.20.10` : premier saut `192.168.10.1` (le routeur), second saut le PC de l'IT.

> <img width="1080" height="1350" alt="slide-08" src="https://github.com/user-attachments/assets/26925a99-dfe9-4aab-b200-cab9661b3b98" />


### 4. Filtrage avec une ACL étendue
Une ACL nommée interdit tout trafic du réseau Compta vers le réseau IT et laisse passer le reste. Elle est appliquée en entrée sur la sous-interface du VLAN 10.

```
ip access-list extended BLOQUE-COMPTA-VERS-IT
 deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
 permit ip any any
interface gigabitEthernet 0/0.10
 ip access-group BLOQUE-COMPTA-VERS-IT in
```

Le `permit ip any any` final est indispensable : sans lui, le refus implicite de fin d'ACL couperait tout le trafic.

Résultat : le `ping` de PC0 vers PC1 renvoie `Destination host unreachable`, émis par le routeur (`192.168.10.1`) lui-même. Le paquet n'atteint jamais le poste IT.

> <img width="1080" height="1350" alt="slide-09" src="https://github.com/user-attachments/assets/0de704e7-cdbb-404e-abb9-d9294c7c3f6a" />


### 5. Sauvegarde
Configuration enregistrée sur le routeur et le switch avec `write memory`, projet conservé dans `labo-vlan-acl.pkt`.

## Problème rencontré et solution

| Problème | Cause | Solution |
|---|---|---|
| Le VLAN 10 n'avait pas de passerelle ; la sortie de `show ip interface brief` ne montrait que `Gig0/0.20` | La sous-interface `Gig0/0.10` n'avait pas été créée (commande sautée) | Relire la sortie de `show ip interface brief`, repérer la ligne manquante, recréer la sous-interface, puis revérifier |

## Ce que j'ai appris

- **Segmenter** un réseau avec des VLAN pour limiter la propagation d'un incident.
- Faire communiquer des VLAN avec un routeur et des **sous-interfaces 802.1Q** sur un lien trunk.
- Écrire une **ACL étendue**, comprendre le **masque inversé** et le refus implicite final.
- Lire des indices dans un `ping` : un `TTL` de 127 au lieu de 128 révèle un routeur traversé.
- **Vérifier** chaque étape (`show vlan brief`, `show ip interface brief`, `tracert`) avant de passer à la suivante, et sauvegarder les configurations.

📎 [Télécharger le fichier Packet Tracer du lab](labo-vlan-acl.pkt)

[← Retour à l'accueil](./)
