# Configuration d'un pare-feu réseau avec iptables

**Domaine :** Sécurité réseau / Administration système
**Environnement :** Lab personnel — Kali Linux (pare-feu), Windows (client)

## Contexte et objectif

`iptables` filtre chaque paquet réseau en le comparant à une liste de règles lues dans l'ordre, jusqu'à trouver une correspondance. L'objectif de ce projet était de construire une politique de sécurité complète et cohérente — de la remise à zéro du système jusqu'au blocage par défaut — en n'autorisant explicitement que les services nécessaires.

## Démarche

**1. Remise à zéro et vérification de l'état initial**
Avant toute configuration, j'ai vidé les chaînes existantes (`iptables -F`) pour repartir d'une base saine et propre, puis vérifié l'état des tables avec `iptables -L -n -v`.

<img width="754" height="512" alt="image" src="https://github.com/user-attachments/assets/0ec3907b-075e-47da-967a-0adc18f0e31d" />
<img width="754" height="547" alt="image" src="https://github.com/user-attachments/assets/f9876944-cf0e-4ce5-8d34-547e79c8efe8" />


**2. Gestion des connexions établies (stateful inspection)**
Première règle posée : autoriser les paquets appartenant à des connexions déjà établies ou liées (`ESTABLISHED,RELATED`). Sans cette règle, le pare-feu bloquerait les réponses légitimes à des requêtes sortantes, rendant la machine inutilisable.

**3. Restriction de l'accès SSH**
Le port 22 a été ouvert uniquement pour l'adresse IP de la machine Windows, plutôt que pour l'ensemble du réseau — réduisant la surface d'attaque à une seule source de confiance identifiée.

<img width="697" height="322" alt="image" src="https://github.com/user-attachments/assets/e4f6296a-00ff-40ce-a111-11e72f6786a4" />

<img width="755" height="273" alt="image" src="https://github.com/user-attachments/assets/3f87c62b-744c-429b-9a01-35f4bf07e60d" />

**4. Ouverture contrôlée du service web**
Le port 80 (HTTP) a été autorisé pour tous les utilisateurs, ce service étant destiné à être public.

<img width="755" height="424" alt="image" src="https://github.com/user-attachments/assets/e4d05fb5-808f-4ba6-b8e5-dd5d92fb7b17" />
<img width="755" height="424" alt="image" src="https://github.com/user-attachments/assets/99d86354-2a0c-4c18-94fb-42a21850c608" />


**5. Blocage du ping et d'une IP spécifique**
Le protocole ICMP a été bloqué pour éviter que la machine ne réponde aux requêtes ping — une mesure simple pour la rendre moins visible sur le réseau — et une IP source précise a été totalement bloquée.

*[Capture : tentative de ping échouée avec délai d'attente dépassé]*

**6. Journalisation et politique de fermeture par défaut**
Toute connexion ne correspondant à aucune règle précédente est d'abord enregistrée dans les logs (`LOG --log-prefix`), puis la politique par défaut de la chaîne INPUT est basculée sur `DROP` — fermant explicitement tout ce qui n'a pas été autorisé.

## Résultat

Le pare-feu final autorise uniquement SSH depuis une IP de confiance, HTTP pour tout le monde, et les réponses aux connexions déjà établies — tout le reste étant journalisé puis rejeté silencieusement.

## Analyse et remédiation

Ce TP illustre le principe de **liste blanche** (whitelist) en sécurité réseau : plutôt que de bloquer ce qu'on identifie comme dangereux, on n'autorise que ce qui est explicitement nécessaire, et tout le reste est refusé par défaut. Deux points d'analyse importants :

- **L'ordre des règles est déterminant** : `iptables` applique la première règle correspondante et arrête la lecture — une règle mal placée peut rendre les suivantes inopérantes.
- **DROP vs REJECT** : `DROP` ignore silencieusement le paquet (favorise la furtivité, complique la reconnaissance par un attaquant), tandis que `REJECT` renvoie une erreur explicite à l'émetteur. Le choix entre les deux dépend du compromis souhaité entre discrétion et diagnostic réseau.

## Compétences mobilisées

`iptables` `Filtrage réseau stateful` `Politique de sécurité par défaut (whitelist)` `Kali Linux` `Diagnostic réseau (ping, SSH, HTTP)`
