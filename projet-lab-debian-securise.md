# Lab : serveur Linux Debian sécurisé, de zéro

![Debian](https://img.shields.io/badge/Debian-13-A81D33?logo=debian&logoColor=white)
![VirtualBox](https://img.shields.io/badge/VirtualBox-183A61?logo=virtualbox&logoColor=white)
![Bash](https://img.shields.io/badge/Bash-4EAA25?logo=gnubash&logoColor=white)
![SSH](https://img.shields.io/badge/SSH-clés%20ed25519-000000)
![UFW](https://img.shields.io/badge/Pare--feu-UFW-E95420)
![cron](https://img.shields.io/badge/Automatisation-cron-6C757D)

## Objectif

Monter un serveur Linux sécurisé à partir de zéro, en ligne de commande, et appliquer les bases de l'administration système : utilisateurs, groupes, droits, services, pare-feu, accès distant par clé, sauvegarde automatisée.

## Contexte et choix technique

J'ai d'abord commencé ce parcours avec Windows Server 2022 et Active Directory (domaine `lab.local`, unités d'organisation Direction, Compta, IT). Mon PC (12 Go de RAM, disque dur mécanique) ne tenait pas la charge : VM figées, assistants graphiques inutilisables.

J'ai donc adapté ma méthode plutôt que d'abandonner : **Debian sans interface graphique**, qui consomme une fraction des ressources, et un accès par **SSH depuis mon poste**.

## Environnement

| Élément | Valeur |
|---|---|
| Hyperviseur | Oracle VirtualBox |
| Système | Debian 13, installation en mode texte, sans bureau |
| Ressources | 1 vCPU, 1 Go de RAM, 10 Go de disque |
| Réseau | NAT, redirection de port 2222 (hôte) vers 22 (VM) |
| Accès | SSH depuis PowerShell |

Après installation, toute la VM tourne avec environ **283 Mo de RAM utilisés** sur 967 Mo (`free -h`).

> 📸 **Capture à insérer ici :** résultat de `free -h` montrant la mémoire utilisée.

## Réalisations

### 1. Utilisateurs, groupes et droits
- Création d'un utilisateur `marie` et d'un groupe `direction` (`adduser`, `addgroup`, `usermod -aG`).
- Création d'un dossier `/srv/direction` réservé au groupe : propriétaire `root`, groupe `direction`, droits `770` (`chown`, `chmod`).
- Test : Marie (membre du groupe) peut créer un fichier ; mon compte, non membre, reçoit « Permission non accordée ». C'est le principe du **moindre privilège**.

> 📸 **Capture à insérer ici :** `ls -ld /srv/direction` (droits `drwxrwx---`) et le refus d'accès.

### 2. Surveillance des services et des connexions
- `systemctl status ssh` : service actif, 8 Mo de RAM.
- `last` : lecture de l'historique des connexions (distinction `pts` pour SSH ou `su`, `tty` pour la console).

> 📸 **Capture à insérer ici :** sortie de `last -n 10`.

### 3. Pare-feu UFW
- Politique par défaut : **tout refuser en entrée**, autoriser la sortie.
- Seul le port `22/tcp` (SSH) est ouvert, règle ajoutée **avant** l'activation pour ne pas me couper de la machine.

> 📸 **Capture à insérer ici :** `ufw status verbose`.

### 4. Accès SSH par clé, sans mot de passe
- Génération d'une paire de clés `ed25519` sur mon poste, dépôt de la clé publique dans `~/.ssh/authorized_keys` (droits `700` sur le dossier, `600` sur le fichier).
- Désactivation de l'authentification par mot de passe (`PasswordAuthentication no`, `KbdInteractiveAuthentication no`), config validée avec `sshd -t` puis rechargée.
- Vérification : une connexion sans clé est refusée avec `Permission denied (publickey)`.

> 📸 **Capture à insérer ici :** le refus `Permission denied (publickey)`.

### 5. Sauvegarde automatisée
- Script Bash `sauvegarde.sh` qui archive `/srv/direction` dans `~/sauvegardes` avec la date (`tar`).
- `umask 077` dans le script : les archives sont privées dès leur création.
- Planification avec `cron` (`0 2 * * *`, chaque nuit à 2 h), journal des exécutions dans `journal.log` (sortie standard et erreurs).
- Test intermédiaire toutes les minutes pour valider le fonctionnement avant de passer à l'horaire réel.

```bash
#!/bin/bash
umask 077
DATE=$(date +%Y-%m-%d)
mkdir -p ~/sauvegardes
tar -czf ~/sauvegardes/direction-$DATE.tar.gz /srv/direction
echo "Sauvegarde créée : direction-$DATE.tar.gz"
```

> 📸 **Capture à insérer ici :** `crontab -l` et le contenu de `journal.log`.

### 6. Instantané
Instantané VirtualBox `Debian-base-OK` pris une fois la configuration validée, pour pouvoir revenir en arrière en cas d'erreur.

## Problèmes rencontrés et solutions

| Problème | Cause | Solution |
|---|---|---|
| Accès refusé au dossier `direction` juste après avoir ajouté mon compte au groupe | Les groupes ne sont relus qu'à l'ouverture d'une **nouvelle session** | Me déconnecter et me reconnecter ; vérification avec `id` |
| `usermod aG ...` affiche la page d'aide | Tiret oublié devant l'option (`-aG`) | Lire la première ligne de l'aide (`Utilisation :`) et corriger la syntaxe |
| Sauvegarde lisible par tous (`-rw-rw-r--`) | Droits par défaut : la copie contournait la protection du dossier source | `chmod 600` sur l'archive et `700` sur le dossier, puis `umask 077` dans le script |
| « Aucun fichier ou dossier » sur `srv/direction` et `~labo` | Chemin relatif au lieu d'absolu, et `~labo` au lieu de `~/labo` | Distinguer chemin absolu (`/srv/...`) et relatif ; `~/` désigne mon dossier personnel |
| VM Windows Server trop lente | Disque dur mécanique saturé, RAM limitée | Changement de stratégie : Debian en ligne de commande, accès SSH |

## Ce que j'ai appris

- Appliquer le **moindre privilège** aux droits de fichiers, y compris aux **copies** (sauvegardes).
- Durcir un accès distant : clés SSH, mot de passe désactivé, pare-feu fermé par défaut.
- Automatiser une tâche d'administration avec Bash et `cron`, et **journaliser** son exécution.
- Diagnostiquer une erreur en lisant précisément le message plutôt qu'en réessayant au hasard.
- Adapter son environnement de travail à ses contraintes matérielles.

[← Retour à l'accueil](./)
