# Déploiement d'un réseau Ethereum privé multi-nœuds

**Domaine :** Infrastructure blockchain / Administration réseau
**Environnement :** Lab personnel — Ubuntu, Geth (go-ethereum) v1.12.0, consensus Clique (Proof-of-Authority)

## Contexte et objectif

Un réseau Ethereum privé permet d'expérimenter avec les mécanismes internes de la blockchain (consensus, comptes, transactions, communication pair-à-pair) sans dépendre du réseau public et sans coût en gas réel. L'objectif de ce projet était de déployer un réseau privé à deux nœuds, configurés en consensus Clique (Proof-of-Authority — adapté aux réseaux privés/de test, car il ne nécessite pas de puissance de calcul dédiée au minage), de les faire communiquer entre eux, puis d'interagir avec la chaîne via la console JavaScript de Geth.

## Démarche

**1. Définition du bloc genèse (`genesis.json`)**
J'ai configuré le fichier de genèse définissant les règles du réseau : chain ID personnalisé (`12345`), toutes les forks Ethereum historiques activées dès le bloc 0 pour un réseau moderne, un consensus Clique avec une période de 5 secondes entre blocs, et une allocation de solde initial à deux comptes de test.

Fichier de genèse utilisé :

```json
{
  "config": {
    "chainId": 12345,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "istanbulBlock": 0,
    "muirGlacierBlock": 0,
    "berlinBlock": 0,
    "londonBlock": 0,
    "arrowGlacierBlock": 0,
    "grayGlacierBlock": 0,
    "clique": {
      "period": 5,
      "epoch": 30000
    }
  },
  "difficulty": "1",
  "gasLimit": "800000000",
  "alloc": {
    "0x4b26790c1F501cbee25B43b996B917BC4EF188f0": { "balance": "100000000000000000000000" },
    "0x812Ee8C2283aE2D3b9b361b93EE6F18C85A1194A": { "balance": "120000000000000000000000" }
  }
}
```

**2. Génération d'un nœud bootnode**
Pour permettre aux nœuds de se découvrir sur le réseau privé, j'ai généré une clé de nœud dédiée et lancé un bootnode avec `bootnode -nodekey boot.key`, produisant une URL `enode://...` — l'identifiant réseau utilisé ensuite par les autres nœuds pour se connecter entre eux.

<img width="1166" height="91" alt="3" src="https://github.com/user-attachments/assets/1f7dabb0-5e69-4c32-b2ff-ebe1fd51e1b2" />


**3. Démarrage du premier nœud (node1)**
J'ai initialisé et lancé le premier nœud avec `geth`, en spécifiant le répertoire de données, l'ID réseau, le déverrouillage d'un compte via mot de passe, et un port RPC dédié.

**4. Démarrage du second nœud (node2) et connexion au réseau**
J'ai lancé un second nœud en le pointant vers l'URL du bootnode généré à l'étape 2, avec un port et un répertoire de données distincts. Les logs de démarrage confirment l'initialisation correcte du protocole Ethereum, la reconnaissance du consensus Clique et du chain ID configuré.

<img width="1280" height="800" alt="1" src="https://github.com/user-attachments/assets/8e1e1be2-18e7-4a5e-9f71-15c02538b338" />
<img width="1280" height="800" alt="2" src="https://github.com/user-attachments/assets/e820569a-1420-4e86-aa50-c896895b93ff" />


Une première tentative avec un port RPC mal formé (`85522`, hors de la plage valide 0-65535) a provoqué une erreur fatale au démarrage — corrigée en ajustant le port à une valeur valide.

<img width="1280" height="800" alt="Capture d’écran du 2025-12-02 18-40-23" src="https://github.com/user-attachments/assets/6f51bd61-cd23-42ce-bcf0-1247a51a828e" />


**5. Connexion à la console et exploration de la chaîne**
En attachant la console JavaScript de Geth au nœud via son socket IPC (`geth attach node1/geth.ipc`), j'ai exploré l'état du réseau : nombre de pairs connectés (`net.peerCount`), liste des comptes du nœud (`eth.accounts`), numéro de bloc courant (`eth.blockNumber`), et solde des comptes (`eth.getBalance`, converti en ether avec `web3.fromWei`).

<img width="1207" height="554" alt="4" src="https://github.com/user-attachments/assets/479a04b3-b579-4821-9d99-8a15c5881afa" />


## Résultat

Le réseau privé à deux nœuds a été déployé et configuré avec succès sous consensus Clique. La console Geth a permis de confirmer l'état de la chaîne et d'interagir directement avec les comptes provisionnés dans le bloc de genèse.

## Analyse

Ce projet illustre les briques fondamentales d'une infrastructure blockchain : un bloc de genèse qui fixe les règles du jeu (consensus, comptes initiaux, paramètres de fork), un mécanisme de découverte pair-à-pair (bootnode) qui permet aux nœuds de se trouver sans configuration réseau centralisée, et une interface de gestion (console JavaScript) qui permet d'interroger et de piloter le nœud localement.

Un point pratique observé pendant le déploiement : les erreurs de configuration réseau (port invalide, chemin IPC incorrect) sont détectées au démarrage avec des messages explicites — un comportement utile pour le diagnostic, mais qui souligne aussi l'importance de valider systématiquement les paramètres réseau avant tout déploiement, y compris en environnement de test.

**Bonne pratique de sécurité :** les clés privées, keystores et mots de passe générés pour ce réseau de test restent strictement locaux et ne sont jamais partagés ou publiés, même dans un contexte de démonstration — une discipline à conserver identique pour tout déploiement, test ou production.

## Compétences mobilisées

`Ethereum / Geth (go-ethereum)` `Consensus Clique (Proof-of-Authority)` `Configuration réseau pair-à-pair` `Console JavaScript Web3` `Administration système Linux`
