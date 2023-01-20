# Installation et configuration de Element Web

- [Installation et configuration de Element Web](#installation-et-configuration-de-element-web)
  - [Installation de Element Web](#installation-de-element-web)
  - [Configuration de Element Web](#configuration-de-element-web)
  - [Se faire Héberger sur nginx](#se-faire-héberger-sur-nginx)

## Installation de Element Web

Pour installer Element Web, il suffit de télécharger la dernière version</br>
disponible sur [ce dépôt git.](https://github.com/vector-im/element-web/releases/tag/v1.11.17) puis de suivre la [procédure](https://github.com/vector-im/element-web/blob/develop/README.md) fournie avec.

## Configuration de Element Web

Le seul fichier à modifier pour la configuration est nommé config.json</br>
et se situe dans le dossier source de Element Web. Les choses</br>
interessante à modifier serait :

        "m.homeserver": {
            "base_url": "https://matrix-client.matrix.org",
            "server_name": "matrix.org"
        }

où base_url est le serveur de base vers lequel element va chercher</br>
à se connecter. On peut également changer le nom.

## Se faire Héberger sur nginx

Rendez-vous [ici.](Installation_serveur_HTTP_NGINX.md#hebrger-un-site-sur-nginx)
