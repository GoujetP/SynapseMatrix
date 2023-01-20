# Installation de Nginx, un serveur HTTP

- [Installation de Nginx, un serveur HTTP](#installation-de-nginx-un-serveur-http)
  - [Installation et Utilisation locale de nginx](#installation-et-utilisation-locale-de-nginx)
  - [Accès au service depuis la machine d'un autre réseau](#accès-au-service-depuis-la-machine-dun-autre-réseau)
  - [Hebrger un site sur nginx](#hebrger-un-site-sur-nginx)

## Installation et Utilisation locale de nginx

Sur une machine, nous allons installer `ngnix`, un serveur HTTP.
Pour cela, il nous suffit de taper la commande :

```bash

    login@phys:~$ sudo apt update
    login@phys:~$ sudo apt install nginx

```

En cas d'échec du démarrage du service, pensez à désactiver le service</br>
apache2.service s'il est installé.</br>
Utilisez la commande `systemctl stop apache2.service`. Ensuite, activer</br>
le service nginx avec `systemctl start nginx`.

Maintenant installons `curl`, un client HTTP. Ilpourra donc récupérer les</br>
pages proposées par nginx.

```bash

    login@phys:~$ sudo apt update
    login@phys:~$ sudo apt install curl

```

En local, la seule commande 'curl http://localhost fonctionne'. En</br>
revanche pour avoir accès à la page http://localhost d'une machine</br>
distante sur laquelle nginx est installé biensûr, il faut ajouter</br>
quelques option. Dans notre cas la commande sera :

```bash

    login@phys:~$ curl -x 192.168.194.3:80 http://localhost

    ## L'adresse 192.168.94.3 représente la machine distante
    ## l'option -x étant pour préciser un proxy par lequel
    ## passé. Ici, il s'agit de notre vm au port 80

```

## Accès au service depuis la machine d'un autre réseau

Pour accéder à la page localhost d'une machine distante n'ayant pas</br>
de réseau commun avec la machine physique, c'est impossible. En effet</br>
la machine distante est sur un réseau privé.</br>
Néanmoins si vous avez un réseau commun avec une machine qui partage</br>
elle-même un réseau commun avec la machine distante, alors une</br>
solution existe. On appelera la machine intermédiaire 'inter' et on</br>
utilisera l'option -L de ssh.

Lors de la connexion ssh à une machine distante on peut préciser à</br>
notre machine physique un port spécifique de la machine distante à écouter.

```bash

    login@phys:~$ ssh -L 8080:localhost:80 inter

    # L'option -L se lit : le port 8080 de notre machine
    # physique écoute à l'adresse localhost:80 de la machine inter.

```

Pour configurer cette redirection de port de manière persistante il</br>
faut se rendre dans _.ssh/config_ et ajouter à notre connexion à la</br>
vm l'option : "LocalForward 8080 localhost:80". On peut également faire</br>
une copie de la configuration de vmjump pour en faire une nommée par</br>
exemple tunnel.

En répétant cette manipulation sur 'phys' puis sur 'inter' respectivement</br>
vers 'inter' et 'machine distante' On peut donc finalement taper la</br>
commande 'curl http://localhost:80' et on aura accès au serveur nginx</br>
de la machine distante sur la machine physique.

## Hebrger un site sur nginx

Une procédure très bien expliqué est disponible à [cette adresse.](https://www.linuxshelltips.com/install-nginx-in-linux/)
