Attaque Man-in-the-Middle par empoisonnement ARP

Domaine : Sécurité offensive / Sécurité réseau Environnement : Lab personnel — Kali Linux (attaquant), Windows (cible), réseau isolé

Contexte et objectif

Le protocole ARP ne vérifie pas l'authenticité de ses réponses : n'importe quelle machine du réseau peut prétendre être la passerelle. L'objectif de ce projet était de démontrer concrètement cette faille — depuis l'interception jusqu'à l'exploitation — pour comprendre pourquoi elle reste l'une des attaques réseau locales les plus efficaces, et quelles contre-mesures la neutralisent.

Démarche

1. Empoisonnement de la table ARP À l'aide d'arpspoof, j'ai fait croire simultanément à la machine victime que j'étais la passerelle, et à la passerelle que j'étais la victime — un détournement dans les deux sens pour intercepter tout le trafic sans casser la connexion internet de la cible.

2. Maintien de la connectivité (IP forwarding) Sans transfert de paquets activé sur la machine attaquante, la victime perd immédiatement sa connexion et l'attaque est détectée. J'ai activé le routage IPv4 sur Kali pour que le trafic continue de transiter normalement — condition indispensable pour une interception "transparente".

3. Vérification du détournement Confirmation côté victime (arp -a) que l'adresse MAC associée à la passerelle correspondait désormais à celle de la machine attaquante — preuve que le détournement fonctionnait au niveau de la couche liaison.

4. Interception du trafic (Wireshark) En filtrant le trafic HTTP intercepté, j'ai capturé une requête POST contenant un identifiant et un mot de passe transmis en clair — démonstration directe du risque que représente un protocole non chiffré sur un réseau compromis.

Résultat

L'attaque a permis d'intercepter des identifiants de connexion en clair, sans qu'aucune alerte ne soit visible côté victime au moment de l'interception (seul un examen de la table ARP révèle l'anomalie).

Analyse et remédiation

Ce projet illustre un principe clé en sécurité réseau : la confidentialité des données ne peut pas reposer sur la confiance implicite d'un protocole non authentifié. Deux niveaux de protection s'en dégagent :

Côté application/transport : chiffrer systématiquement les échanges (HTTPS, SSH) pour rendre l'interception inexploitable même si le trafic est détourné.
Côté infrastructure réseau : activer la Dynamic ARP Inspection (DAI) sur les switchs pour bloquer les réponses ARP non légitimes à la source, plutôt que de compter uniquement sur le chiffrement en bout de chaîne.
Compétences mobilisées

ARP spoofing Kali Linux Wireshark Analyse de trafic réseau IP forwarding Sécurité des protocoles

[Captures d'écran à insérer : table ARP avant/après empoisonnement, capture Wireshark du paquet POST]
