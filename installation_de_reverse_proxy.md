# Création et configuration d'un reverse proxy avec nginx

- [Création et configuration d'un reverse proxy avec nginx](#création-et-configuration-dun-reverse-proxy-avec-nginx)
  - [Création d'une machine virtuelle](#création-dune-machine-virtuelle)
  - [Installation et configuration de nginx](#installation-et-configuration-de-nginx)

## Création d'une machine virtuelle

Pour rappel, nous travaillons sur un réseau privé dont la</br>
table des adresses est la suivante :

| Machine | Adresse |
|---------|---------|
Machine de virtualisation | 192.168.194.1
Machine vituelle Matrix | 192.168.194.3
|---------|---------|
Routeur, DNS | 192.168.194.2
Adresses dynamiques (attribuées automatiquement) | 192.168.194.25-192.168.194.128

La création d'une machine servant de reverse proxy vient tout</br>
simplement à refaire la même manipulation que pour la procédure</br>
[ici](Creation_d_une_machine_virtuelle.md), suivi de la partie "Dernière configuration</br>
de la VM" (`et uniquement cette partie !`) de [cette](./Journal02.md) procédure-là.

## Installation et configuration de nginx

Nginx viens remplacer les serveurs apache. Il a une faible utilisation de mémoire.</br>
Et le point important est qu'il inclue le reverse proxy directement.</br>
De plus il est préférable de l'utiliser pour les sites web avec une forte affluence.

**Pour la suite, tout se fait sur la machine créée juste avant.**

Pour installer nginx il faut d'abord le télécharger avec la commande : `apt install nginx`</br>
en étant connecté en super user.

Cette fois-ci, nous ne nous occuperons pas de la page d'accueil du serveur http nginx,</br>
mais de ses fichiers de configuration de reverse proxy.

Pour cela il suffit simplement d'indiquer à nginx sur quel port écouter les requêtes</br>
et une fois fait vers quelle adresse les rediriger.

Toujours en super utilisateur :</br>
Rendez-vous à l'adresse '/etc/nginx/sites-enabled/'

```bash

    root@vm:~$ cd /etc/nginx/sites-enables/
    root@vm:~$ sudo nano mon_site

    ## Ajout d'un fichier mon_site
    ## À l'intérieur, écrire

        server {
            listen 80;

            root /var/www/html

            server_name <nom de serveur>
            location / {
                proxy_pass http://192.168.194.6:8080
            }
        }

```

En écoutant sur le port 80 de la machine ou nginx est installé on accéder à l'adresse</br>
http://192.168.194.6:8080 en recherchant \< nom de serveur\>
