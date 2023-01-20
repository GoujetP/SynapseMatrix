# Installation/Configuration d'une base de donnée avec Postgresql

- [Installation/Configuration d'une base de donnée avec Postgresql](#installationconfiguration-dune-base-de-donnée-avec-postgresql)
  - [Installation et configuration basique d’un serveur de base de données](#installation-et-configuration-basique-dun-serveur-de-base-de-données)
    - [Installer postgresql et vérifier l'état du service](#installer-postgresql-et-vérifier-létat-du-service)
    - [Créer un utlisateur et une base de donnée](#créer-un-utlisateur-et-une-base-de-donnée)
    - [Autoriser une connexion depuis une autre machine du réseau](#autoriser-une-connexion-depuis-une-autre-machine-du-réseau)

## Installation et configuration basique d’un serveur de base de données

Une base de données sert simplement à stocker de l'information et ce peu</br>
importe la manière pourvu qu'elle soit structurée.

[Postgresql](https://fr.wikipedia.org/wiki/PostgreSQL) est un SGBD(système de gestion de base de données relationnelle)</br>
qui propose une utilisation via une interface graphique mais aussi en</br>
ligne de commande.

Nous allons donc expliquer comment l'installer, l'utiliser et le configurer.

### Installer postgresql et vérifier l'état du service

Pour installer postgresql on passe par [apt](Manipulation_d_une_machine_virtuelle.md#installation-doutils).

```bash

    user@vm:~$ su -
    Password:
    root@vm:~\# apt-get install postgresql
    root@vm:~\# exit
    
    ## Une fois installé on test le service
    user@vm:~$ sudo systemctl status postgresql
    [sudo] password for login:  

        ● postgresql.service - PostgreSQL RDBMS
        Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
        Active: active (exited) since Sat 2022-11-26 18:35:22 CET; 52min ago
        Process: 2092 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
    Main PID: 2092 (code=exited, status=0/SUCCESS)

    nov. 26 18:35:22 vm systemd[1]: Starting PostgreSQL RDBMS...
    nov. 26 18:35:22 vm systemd[1]: Finished PostgreSQL RDBMS.

```

### Créer un utlisateur et une base de donnée

Lors de l'installation de postgresql, un utilisateur `postgres` est</br>
créer au même titre que user qu'on uilise dans nos exemple. La</br>
différence est que postgres peut se connecter à une database "postgres"</br>
elle aussi créee lors de l'installation. et c'est via cet utilisateur</br>
que nous pouvons créer de nouvelles bases de données et de nouveaux</br>
utilisateurs pour ces bases.

Pour ce faire on se connecte à postgres via la commande `sudo`, puis</br>
on utilise des commande qui viennent avec l'outils postgres.

```bash
    ## -u pour user, et bash pour faire comme si on ouvrait une interface
    ## en ligne de commande sur cette session.
    user@vm:~$ sudo -u postgres bash

    ## Les options -DRS pour un création facile et -P pour créer un
    ## utiilisateur avec un password et matrix le nom d'utilisateur.
    postgres@vm:/home/user$ createuser -DRS -P matrix

    ## L'option -O pour Owner
    postgres@vm:/home/user$ createdb -O matrix matrix

```

Il est important de noter que la connexion à la base de données matrix</br>
nouvellement créée ne sera pas possible en tant que 'user'. Il faut</br>
passer par le réseau pour se connecter :

```bash

    ## L'option -U donne le nom d'utilisateur pris pour la connexion
    ## L'option -h pour Host sert à désigner à quelle adresse la base
    ## est-elle hébergée. 
    user@vm:~$ psql -U matrix -h localhost

```

### Autoriser une connexion depuis une autre machine du réseau

Juste au dessus nous venons de voir qu'il était possible de passer</br>
par le réseau pour se connecter à une base de données en se servant</br>
d'un nom d'utilisateur bien précis.

Voyons maintenant dans cette partie, comment se connecter depuis une</br>
autre machine du réseau sur la base de données de la machine qui</br>
l'héberge.

Nommons 'dbServer' la machine qui héberge la base de données et 'client'</br>
qui sera la machine essayant de se connecter à la base.

Précédemment on a utilisé avec l'option '-h' de la commande 'psql'</br>
l'adresse 'loacalhost'. Vous remarquerez que si l'on essaye de se</br>
connecter depuis 'client' à la base de données de 'dbServer' on</br>
obtient un erreur.</br>
L'origine de cette erreur est dû au fait que la base de données n'écoute</br>
que les requêtes à l'adresse localhost(127.0.0.1) de sa machine.</br>

Pour corriger cela, on doit modifier le fichier de configuration</br>
postgresql.conf.

```bash

    ## La valeur 13 correspond à la version de postgresql que vous avez
    ## Elle peut tout à fait être inférieur ou supérieur à 13.
    login@dbServer:~$ sudo nano /etc/postresql/13/main/postgresql.conf

    ## Dans la rubrique - Connection Settings - vers le début du fichier,
    ## changer le paramètre listen_addresses

    listen_addresses = 'localhost, 192.168.194.3'

    ## Ici 192.168.194.3 représente l'adresse de dbServer sur le réseau
    ## commun à client.

```

On peut également changer le port à écouter par défaut juste en bas.</br>
'port = 5432'

Nous pouvons désormais accéder au SGBD depuis 'client', mais toujours</br>
pas à la base de données matrix créée plus haut.</br>
Pour pouvoir s'y connecter il nous faut toucher à un deuxième fichier</br>
du même répertoire 'pg_hba.conf'.

```bash

    login@dbServer:~$ sudo nano /etc/postresql/13/main/pg_hba.conf

    ## La ligne en début de fichier nous précise la syntaxe à utiliser

    # host          DATABASE  USER    ADDRESS  METHOD  [OPTIONS]

    host            marix     matrix  client   md5
```

md5 étant une méthode d'uathentification chiffrée.

Enfin il faut redémarrer le service postgresql avec la commande

```bash

    login@dbServer:~$ sudo systemctl restart postgresql.service

```
