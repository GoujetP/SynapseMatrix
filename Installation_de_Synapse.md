# Installation et configuration du serveur Synapse

- [Installation et configuration du serveur Synapse](#installation-et-configuration-du-serveur-synapse)
  - [Installation de matrix-synapse](#installation-de-matrix-synapse)
  - [Configuration de Synapse pour Postgres](#configuration-de-synapse-pour-postgres)
  - [Création d'utilisateurs Synapse](#création-dutilisateurs-synapse)
  - [Variante avec base de donnée distante](#variante-avec-base-de-donnée-distante)

## Installation de matrix-synapse

Pour installer synapse sur une machine tournant sous Debian, [matrix.org](https://matrix.org)
nous</br>
fournit une [procédure d'installation.](https://matrix-org.github.io/synapse/latest/setup/installation.html#matrixorg-packages)

Il suffit donc de la suivre.

`À SAVOIR :`

- A l’installation, le gestionnaire de paquets vous demande le nom de votre instance.
  Vous devez indiquer machine-de-virtu.iutinfo.fr:8008. Attention à bien indiquer le
  port 8008 et à remplacer machine-de-virtu par le nom de votre machine de
  virtualisation (par exemple ayou03).

- Le serveur écrira ses messages à destination de
  l’administrateur (les logs) dans le fichier /var/log/matrix-synapse/homeserver.log.

## Configuration de Synapse pour Postgres

Synapse est configuré par défaut avec sqlite qui n'est pas suffisant</br>
pour une mise en production. Utiliser donc postgres est plus adapté.</br>
Voyons comment faire.

Une fois de plus, [matrix.org](https://matrix.org) nous fournit une procédure déjà complète [ici](https://matrix-org.github.io/synapse/latest/postgres.html)

`À SAVOIR :`

- Dans la procédure, un utilisateur `synapse_user` et une base `synapse`</br>
  sont crées. Nous créerons une base nommée `matrix` et un utilisateur</br>
  `matrix` avec un mot de passe étant `matrix!`

Redémarrer le service synapse avec la commande  `synctl restart.`

## Création d'utilisateurs Synapse

Pour créer de nouveaux users sur dans la base de donnée de synapse,</br>
il faut tout simplement ajouter au fichier homeserver.yaml situé par</br>
défaut à l'adresse /etc/matrix-synapse/homeserver.yaml les paramètres :</br>
`enable_registration_without_verification: true`, `registration_shared_secret: `\<secret word\>

## Variante avec base de donnée distante

Si vous séparez base de données et serveur synapse, vous n'aurez besoin de</br>
modifier qu'une seule ligne dans le fichier homeserver.yaml.

```bash

    login@dbServer:~$ sudo nano /etc/matrix-synapse/homeserver.yaml

    ## Changez le paramètre host

    host: db

    ## Avec db le nom de la machine hébergeant la base de données

```

Pour la configuration à faire côté 'db', référez-vous à [ceci.](installation_Configuration_BDD.md#autoriser-une-connexion-depuis-une-autre-machine-du-réseau)
