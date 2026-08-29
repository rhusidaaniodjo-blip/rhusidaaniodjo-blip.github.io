# Exploitation de vulnérabilités web sur DVWA

**Domaine :** Sécurité offensive / Sécurité applicative web
**Environnement :** Lab personnel — Kali Linux, Burp Suite, DVWA (Damn Vulnerable Web Application)

## Contexte et objectif

DVWA est une application web volontairement vulnérable, conçue pour s'entraîner légalement aux techniques de test d'intrusion. L'objectif de ce projet était d'exploiter plusieurs classes de vulnérabilités web courantes — force brute, upload de fichiers malveillants, contournement de CAPTCHA — pour comprendre concrètement comment une validation insuffisante côté serveur peut être détournée, même quand l'interface utilisateur semble bien protégée.

## Démarche

**1. Attaque par force brute (niveau Low)**
Après avoir identifié les comptes utilisateurs existants par simple exploration de répertoire, j'ai configuré Burp Suite en proxy pour intercepter les requêtes de connexion. À l'aide du module Intruder en mode "Cluster Bomb", j'ai testé toutes les combinaisons entre la liste d'utilisateurs identifiés et une liste de mots de passe courants, en repérant les connexions réussies grâce à une différence de longueur de réponse HTTP — signal fiable pour distinguer un échec d'une authentification valide.<img width="754" height="434" alt="image" src="https://github.com/user-attachments/assets/f15c745c-bf10-483d-bdad-788652a87b68" />



**2. Upload de fichier malveillant (niveaux Low et Medium)**
En niveau faible, j'ai téléversé un script PHP exécutant des commandes système via un paramètre d'URL, révélant l'absence totale de contrôle sur les fichiers acceptés. Au niveau medium, où le serveur vérifie l'extension et le type MIME du fichier, j'ai contourné cette protection en renommant le fichier en `.jpg` et en modifiant le Content-Type intercepté via Burp Suite (`application/x-php` → `image/jpeg`) — démontrant qu'une validation basée uniquement sur des métadonnées côté client reste facilement falsifiable.<img width="754" height="384" alt="image" src="https://github.com/user-attachments/assets/73929604-e355-4053-8c18-8cee555ee931" />


**3. Contournement de CAPTCHA (niveaux Low et Medium)**
Au niveau faible, l'analyse du flux de requêtes avec le Repeater de Burp Suite a révélé qu'il était possible de sauter directement à l'étape de validation sans jamais résoudre le CAPTCHA. Au niveau medium, où l'application vérifie un état interne `passed_captcha`, j'ai intercepté la requête et forcé cette variable à `true` manuellement, changeant le mot de passe sans aucune interaction réelle avec le module de vérification.<img width="754" h<img width="753" height="402" alt="image" src="https://github.com/user-attachments/assets/06b25516-b846-46ba-ad94-abd181bc498f" />
eight="402" alt="image" src="https://github.com/user-attachments/assets/656e819f-902c-46ae-bc8f-bb9be4768898" />  <img width="755" height="424" alt="image" src="https://github.com/user-attachments/assets/9ffb8112-b2ac-4a44-8154-231f2abc5e64" />



## Résultat

Les trois vulnérabilités ont été exploitées avec succès : exécution de commandes arbitraires sur le serveur via l'upload de fichier, contournement complet de la protection anti-bot, et récupération d'identifiants par force brute automatisée.
<img width="754" height="406" alt="image" src="https://github.com/user-attachments/assets/2b69fdba-2254-4f41-a2fb-9b3048e842af" />

## Analyse et remédiation

Le fil conducteur de ce projet est un principe fondamental de sécurité applicative : **toute validation effectuée uniquement côté client ou via des paramètres manipulables (extension de fichier, Content-Type, variable d'état) est intrinsèquement contournable**. Les axes de remédiation identifiés :

- **Upload de fichiers** : valider le contenu réel du fichier (signature/magic bytes) plutôt que son extension ou son Content-Type déclaré, et stocker les fichiers uploadés hors de la racine web exécutable.
- **CAPTCHA** : la validation doit être vérifiée côté serveur à chaque étape sensible, jamais déduite d'un état transmis par le client.<img width="754" height="372" alt="image" src="https://github.com/user-attachments/assets/cba0ac3a-5b10-4f92-81f7-0c7252180187" />

- **Authentification** : limiter le nombre de tentatives de connexion (rate limiting, verrouillage temporaire) pour rendre la force brute impraticable.
![Uploading image.png…]()

## Compétences mobilisées

`Burp Suite (Proxy, Intruder, Repeater)` `Test d'intrusion web` `DVWA` `Analyse de requêtes HTTP` `Contournement de contrôles côté client`

---

