# Chiffrement symétrique sous Linux avec OpenSSL

![OpenSSL](https://img.shields.io/badge/OpenSSL-721412?style=flat-square) ![AES-256](https://img.shields.io/badge/AES--256-721412?style=flat-square) ![Kali Linux](https://img.shields.io/badge/Kali%20Linux-557C94?style=flat-square)

**Domaine :** Cryptographie / Sécurité des données
**Environnement :** Kali Linux, OpenSSL 3.5.4
**Réalisé avec :** Edoun Severin BANKOLE

## Contexte et objectif

Le chiffrement symétrique protège la confidentialité d'un fichier en utilisant une seule et même clé pour le chiffrer et le déchiffrer. L'objectif de ce TP était de mettre en pratique le chiffrement AES-256 avec OpenSSL selon deux approches — clé aléatoire stockée dans un fichier, puis chiffrement basé sur mot de passe — et d'identifier les bonnes pratiques de gestion des clés qui découlent de cette expérience.

## Démarche

**1. Vérification de l'environnement**
Confirmation de la disponibilité d'OpenSSL et de sa version (3.5.4) avant toute manipulation.

**2. Génération d'une clé de chiffrement aléatoire**
Génération d'une clé de 32 octets (256 bits) avec `openssl rand -base64 32`, stockée dans un fichier dédié — la clé qui servira à la fois au chiffrement et au déchiffrement du fichier cible.
<img width="583" height="89" alt="image" src="https://github.com/user-attachments/assets/2ef5bbd0-a778-4f2b-9d17-2373b871be7b" />
<img width="234" height="111" alt="image" src="https://github.com/user-attachments/assets/f0420ba6-f283-45f7-8caf-2c1f6764efa8" />
<img width="755" height="49" alt="image" src="https://github.com/user-attachments/assets/58b357c6-6215-429b-950e-dc619c923f9f" />


**3. Chiffrement et déchiffrement d'un fichier**
Chiffrement d'un fichier texte à l'aide de la clé générée, produisant un fichier binaire illisible sans la clé. Le déchiffrement avec la même clé restitue exactement le contenu original — vérifié par comparaison directe entre le fichier source et le fichier déchiffré.

<img width="755" height="210" alt="image" src="https://github.com/user-attachments/assets/c60b93aa-74c2-4d21-8dd1-893d491d6613" />


**4. Chiffrement basé sur mot de passe**
En alternative à la clé aléatoire, chiffrement du fichier avec `openssl enc -aes-256-cbc -salt`, où la clé de chiffrement est dérivée d'un mot de passe fourni. Cette méthode intègre un salt (valeur aléatoire) mêlé au mot de passe pour dériver la clé réelle, empêchant que le même mot de passe produise toujours le même résultat chiffré.

<img width="755" height="206" alt="image" src="https://github.com/user-attachments/assets/0b0691a4-9c8e-43f9-9c88-527d59edfc81" />


**5. Étude de la sécurité et du stockage des clés**
Génération d'une clé stockée dans un fichier aux permissions restreintes (`chmod 600`), utilisée ensuite pour chiffrer un autre fichier — mettant en évidence qu'une clé mal protégée annule tout l'intérêt du chiffrement, quelle que soit sa robustesse algorithmique.
<img width="755" height="566" alt="image" src="https://github.com/user-attachments/assets/fcab05ba-9e33-446a-a253-643eb1de3cb0" />


## Résultat

Les deux approches de chiffrement (clé aléatoire et mot de passe) ont été mises en œuvre avec succès, avec vérification systématique que le déchiffrement restitue fidèlement le contenu original.
<img width="754" height="150" alt="image" src="https://github.com/user-attachments/assets/15437922-1fa9-4a3c-8c23-b64e007b30b8" />
<img width="672" height="132" alt="image" src="https://github.com/user-attachments/assets/0177340b-9b3a-4133-88ef-4a02a50aa090" />

## Analyse

Ce TP met en évidence un principe central de la cryptographie appliquée : **la robustesse d'un algorithme de chiffrement ne vaut que ce que vaut la protection de sa clé**. AES-256 est un algorithme solide, mais une clé stockée en clair, avec des permissions trop larges ou transmise par un canal non sécurisé, réduit à néant cette robustesse. Le message d'avertissement d'OpenSSL sur la dérivation de clé (recommandant `-iter` ou `-pbkdf2`) illustre également que les paramètres par défaut d'un outil ne sont pas toujours les plus sûrs — une vérification active des recommandations de sécurité reste nécessaire à chaque utilisation.

**Bonnes pratiques de gestion des clés retenues :**
- Ne jamais stocker une clé en clair avec les données qu'elle protège
- Restreindre strictement les droits d'accès aux fichiers de clés
- Utiliser des clés suffisamment longues et générées aléatoirement
- Renouveler régulièrement les clés (rotation)
- Sauvegarder les clés de manière sécurisée, séparément des données chiffrées
- Éviter tout partage de clé par un canal non sécurisé

## Compétences mobilisées

`OpenSSL` `Chiffrement AES-256` `Dérivation de clé par mot de passe` `Gestion sécurisée des clés` `Kali Linux`

---
[← Retour à l'accueil](index.md)
