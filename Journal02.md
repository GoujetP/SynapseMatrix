# Journal 02 : Dernière configuration de la VM & installation basique d'un serveur de base de données

## Dernière configuration de la VM

1. **Changement du nom de domaine**

```bash
    # Tout d'abord il faut changer le nom de manière persistante dans le fichier /etc/hostname

    # Accès au fichier
    login@vm:~$ nano /etc/hostname

    # On remplace debian par matrix
    # On change galement le nom de notre adresse dans /etc/hosts
    login@vm:~$ nano /etc/hosts

    # On redémarre pour que les modifications soient appliquées en root
    login@vm:~$ su -
    Password:
    root@vm:~# reboot
```

Pour vérifier, on peut essayer la commande suivante : 

```bash
    login@vm:~$ ping matrix
    PING matrix (127.0.1.1) 56(84) bytes of data.
    64 bytes from matrix (127.0.1.1): icmp_seq=1 ttl=64 time=0.158 ms
    64 bytes from matrix (127.0.1.1): icmp_seq=2 ttl=64 time=0.063 ms
    ^C
    --- matrix ping statistics ---
    2 packets transmitted, 2 received, 0% packet loss, time 1019ms
    rtt min/avg/max/mdev = 0.063/0.110/0.158/0.047 ms
```

2. **Installation et configuration de la commande `sudo`**

Pour installer sudo il suffit de la télécharger via apt

```bash
    root@vm:~# apt install sudo

    # On ajoute ensuite user à la liste des sudoers
    root@vm:~# adduser user sudo
```

3. **Configuration de la synchronisation d'horloge**

Pour synchroniser l'heure d'une machine, on peut demander l'heure exacte à un serveur de temps via le protocole NTP (Network Time Protocole)

Ici, on utilise le serveur de l'univ `ntp.univ-lille.fr`

Le `service` de notre vm gérant le temps s'appelle : `systemd-timesyncd`

```bash
    # Pour attacher notre service au serveur de temps on ajoute l'adresse du ntp à la variable NTP du fichier 
    login@vm:~$ sudo nano /etc/systemd/timesyncd.conf
    [sudo] password for user :

    login@vm:~$ timedatectl
    Local time: jeu. 2022-11-24 16:15:41 CET
           Universal time: jeu. 2022-11-24 15:15:41 UTC
                 RTC time: jeu. 2022-11-24 15:15:42
                Time zone: Europe/Paris (CET, +0100)
    System clock synchronized: yes
              NTP service: inactive
          RTC in local TZ: no

    # Ci-dessous ntp service est à "inactive"
    # Pour activer l'utilisation du ntp, on utilise la commmande suivante

    login@vm:~$ sudo timedatectl set-ntp true
    login@vm:~$ timedatectl
    Local time: jeu. 2022-11-24 16:15:41 CET
           Universal time: jeu. 2022-11-24 15:15:41 UTC
                 RTC time: jeu. 2022-11-24 15:15:42
                Time zone: Europe/Paris (CET, +0100)
    System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no

    # Pour que toutes nos modifications prennent effet, on redémarre le service

    login@vm:~$ sudo systemctl restart systemd-timesyncd

    # Si cela ne fonctionne pas, alors essayer :

    login@vm:~$ sudo systemctl stop systemd-timesyncd
    login@vm:~$ sudo systemctl enable systemd-timesyncd
    login@vm:~$ sudo systemctl start systemd-timesyncd

    # Et revérifier avec :

    login@vm:~$ timedatectl

```

## Installation et configuration basique d’un serveur de base de données

1. **Installer postgresql et vérifier l'état du service**

```bash
    # Installation via apt comme vu précédemment

    login@vm:~$ su -
    Password:
    root@vm:~# apt-get apt-get install postgresql
    root@vm:~# exit
    
    # Une fois installé on test le service
    login@vm:~$ sudo systemctl status postgresql
    [sudo] password for login:  

        ● postgresql.service - PostgreSQL RDBMS
        Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
        Active: active (exited) since Sat 2022-11-26 18:35:22 CET; 52min ago
        Process: 2092 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
    Main PID: 2092 (code=exited, status=0/SUCCESS)

    nov. 26 18:35:22 vm systemd[1]: Starting PostgreSQL RDBMS...
    nov. 26 18:35:22 vm systemd[1]: Finished PostgreSQL RDBMS.

```

2. **Créer un utlisateur et une base de donnée**

Lors de l'installation de postgresql, un utilisateur `postgres` est créer au même titre que login qu'on tuilise depuis le début. La différence est que postgres peut se connecter à une database "postgres" elle aussi créer lors de l'installation. et c'est via cet utilisateur que nous pouvons créer de nouveaux utilisateurs de base de donnée et de nouvelles bases.

Pour ce faire on se connecte a postgres via la commande `sudo` qu'on à déjà vu, puis on utilise des commande qui viennent avec l'outils postgres.

```bash
    # -u pour user, et bash pour faire comme si on ouvrait un bash sur cette session.
    login@vm:~$ sudo -u postgres bash

    # -DRS pour un création facile et -P pour créer un user avec un password et matrix le nom du user.
    postgres@vm:/home/user$ createuser -DRS -P matrix

    OU -P -d -e A FAIRE TODO\!

    # -O matrix  O pour owner
    postgres@vm:/home/user$ createdb -O matrix matrix

    # Pour se connecter en local en passant par le réseau avec localhost
    login@vm:~$ psql -U matrix -h localhost
```
