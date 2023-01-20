# Gérer les machines virtuelles

- [Gérer les machines virtuelles](#gérer-les-machines-virtuelles)
  - [Utilisation de vmiut](#utilisation-de-vmiut)
  - [Console virtuelle](#console-virtuelle)
  - [Configurer et mettre à jour la machine virtuelle](#configurer-et-mettre-à-jour-la-machine-virtuelle)
    - [Configuration de l'interface réseau](#configuration-de-linterface-réseau)
    - [Mise à jour](#mise-à-jour)
    - [Installation d’outils](#installation-doutils)
    - [Changement du nom de domaine](#changement-du-nom-de-domaine)
    - [Installation et configuration de la commande `sudo`](#installation-et-configuration-de-la-commande-sudo)
    - [Configuration de la synchronisation d'horloge](#configuration-de-la-synchronisation-dhorloge)

Pour manipuler des machines virtualbox en ligne de commandes, on</br>
utilisera un script nommé **`vmiut`** permettant d'`utiliser les commandes`</br>
de **`virtualbox`** de manière `simplifiée.`

## Utilisation de vmiut

```bash
    # Pour utiliser vmiut il faut charger l'environnement à chaque nouveau shell avec la commande
    login@virtu$ source /home/public/vm/vm.env

    # Pour afficher l'aide, il suffit d'entrer la commande vmiut sans paramètres

     login@virtu$ vmiut
     Gestion de machines virtuelles simples pour les TP à l'IUT

    usage: vmiut [OPTIONS] COMMANDE [MACHINE]

COMMANDES
    
    doc aide help man             afficher la documentation
    creer make                    créer une VM
    demarrer run start            démarrer une VM
    redemarrer reset restart      redémarrer "brutalement" une VM
    arreter stop                  éteindre "brutalement" une VM
    console show                  afficher la console "physique" d'une VM
    supprimer rm                  supprimer une VM
    detailler conf info           montrer la configuration d'une VM
    localiser locate whereis      afficher la localisation d'une VM
    lister list ls                lister les VMs existantes
    importer add import register  importer une VM précédemment créée avec vmiut
    monter mount                  monter la partition sda1 d'une VM dans /tmp
    demonter umount unmount       démonter la paritition sda1 d'une VM

OPTIONS

    -d, --disque FICHIER     utiliser FICHIER pour $MODELE
    -n, --dry-run            montre les commandes sans les exécuter
    -h, --help               affiche ce message d'aide
    -r, --reseaux RESEAUX    attache la machine aux RESEAUX indiqués
                             (par défaut 'bridged')
    -V, --version            affiche la version du script

```

Pour utiliser vos machines virtuelles, deux solutions:

- utiliser une console virtuelle : simule un clavier et un</br>
  écran qui serait connectés physiquement à la machine virtuelle
- se connecter en SSH.

## Console virtuelle

Pour utiliser la console virtuelle, il faut lancer la commande :

```bash

    login@phys:~$ vmiut console matrix

```

Si vous avez le message d’erreur suivant: 'ERROR: Failed to open display:'

Ceci est dû au fait que la console virtuelle est une application</br>
graphique mais que vous êtes connectés à distance sur une machine.</br>
L’application graphique ne peut donc pas afficher sa fenêtre.

Pour palier à ce problème, nous allons utiliser une fonctionnalité</br>
de SSH qui permet de rediriger une application graphique par la connexion</br>
SSH. Pour cela, arrêtez votre connexion SSH et reconnectez vous avec</br>
la commande suivante :

```bash

    ## Ici l'option -X permet d’effectuer la redirection graphique.
    login@phys:~$ ssh -X matrix

```

Une fois sur la console, connectez vous en tant que user puis passez</br>
root et utilisez les commandes ip addr show et ip route show pour</br>
constater que les paramètres de réseau correspondent bien à ce qui</br>
est attendu.

## Configurer et mettre à jour la machine virtuelle

### Configuration de l'interface réseau

Par défaut, les interfaces réseaux suivent le protocole</br>
[DHCP(Dynamic Host Configuration Protocol)](https://fr.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol)
qui questionne un serveur</br>
[DNS(Le Domain Name System)](https://fr.wikipedia.org/wiki/Domain_Name_System)
pour avoir une adresse sur le réseau.</br>

Cette adresse est donnée dynamiquement et peut donc changer entre deux</br>
connexions au même réseau(lors d'un redémarrage par exemple).

Si on souhaite garder une même adresse tout le temps, ce qui est utile</br>
lorsque l'on déploie des applications sur le réseau qui passent par notre</br>
machine, alors il faut configurer notre interfaces nous-même et cela de</br>
manière persistante(donc pas de changement lors d'un redémarrage).

Pour configurer notre interface, rendez-vous sur notre machine **`via la console`**</br>
car nous allons devoir couper la connexion, or SSH en dépend.

Une fois la console ouverte on passe en root :

```bash
    
    user@vm:~$ su -
    Password:

    ## On se rend sur dans /etc/network/interfaces
    root@vm:~\# nano /etc/network/interfaces

    ## On paramètre notre interface enp0s3 par exemple et on lui donne
    ## l'adresse 192.168.194.3 sur le réseau 192.168.194.0/24 avec un
    ## gateway à l'adresse 192.168.194.2

        allow-hotplug enp0s3
        iface enp0s3 inet static
            address 192.168.194.3/24
            gateway 192.168.194.3

    ## Et on supprime les autres lignes qui parle de enp0s3 en DHCP
    ## Si vous avez des doutes ne supprimez pas, les mettre en commentaire
    ## sera amplement suffisant.

```

Après cela, on peux enregistrer et quitter le fichier, entrer la</br>
commande 'reboot' en root et refaire sa connexion SSH.

_*remarque*_ : il faut également remettre à jour .ssh/config sur la</br>
machine physique.

### Mise à jour

On peux maintenant mettre à jour la machine qui est pour l'instant sur</br>
la version de l'image disque utilisée lors de l'installation et qui</br>
n'est pas forcément à jour.

Pour cela c'est assez simple :

```bash

    user@vm:~$ su -
    Password:
    root@:~\# apt update && apt full-upgrade
    
```

Si Grub apparait et vous demande de choisir un disque alors cochez à</br>
l'aide de la barre d'espacement la partition sur laquelle vous souhaitez</br>
installer Grub.

Enfin entrez la commande `reboot` en **root** pour redémarrer et</br>
appliquer les changements.

### Installation d’outils

Pour installer une application ou un outils sur debian, on passe par</br>
`apt(Advanced Package Tool).` La commande se nomme apt et s'utilise</br>
comme suit :

```bash

    user@vm:~$ su -
    Password:
    root@vm:~\# apt install vim less tree rsync

```

Ce qui installera les outils {vim, less, tree, rsync}.

### Changement du nom de domaine

```bash
    ## Tout d'abord il faut changer le nom de manière persistante
    ## dans le fichier /etc/hostname

    ## Accès au fichier
    user@vm:~$ nano /etc/hostname

    ## On remplace debian par matrix
    ## On change également le nom de notre adresse dans /etc/hosts
    user@vm:~$ nano /etc/hosts

    ## On redémarre pour que les modifications soient appliquées en root
    user@vm:~$ su -
    Password:
    root@vm:~\# reboot
```

Pour vérifier, on peut essayer la commande suivante :

```bash

    user@vm:~$ ping matrix
    PING matrix (127.0.1.1) 56(84) bytes of data.
    64 bytes from matrix (127.0.1.1): icmp_seq=1 ttl=64 time=0.158 ms
    64 bytes from matrix (127.0.1.1): icmp_seq=2 ttl=64 time=0.063 ms
    ^C
    --- matrix ping statistics ---
    2 packets transmitted, 2 received, 0% packet loss, time 1019ms
    rtt min/avg/max/mdev = 0.063/0.110/0.158/0.047 ms

```

### Installation et configuration de la commande `sudo`

La commande 'sudo' permet d'obtenir presque tous les droits super</br>
utilisateur sans pour autant l'être. C'est pratique lorsqu'on veut</br>
pouvoir installer des outils ou modifier des fichiers en gardant</br>
notre environnement de travail.

Pour installer sudo il suffit de la télécharger via apt dont on a</br>
parlé plus haut

```bash

    root@vm:~\# apt install sudo

    ## On ajoute ensuite user à la liste des sudoers
    root@vm:~\# adduser user sudo

```

### Configuration de la synchronisation d'horloge

Pour synchroniser l'heure d'une machine, on peut demander l'heure exacte à un serveur de temps via le protocole NTP (Network Time Protocole)

Ici, on utilise le serveur de l'université de lille pour exemple(`ntp.univ-lille.fr`)

Le `service` de notre machine virtuelle gérant le temps s'appelle : `systemd-timesyncd`

```bash
    ## Pour attacher notre service au serveur de temps on ajoute
    ## l'adresse du ntp à la variable NTP du fichier 
    user@vm:~$ sudo nano /etc/systemd/timesyncd.conf
    [sudo] password for user :

    user@vm:~$ timedatectl
    Local time: jeu. 2022-11-24 16:15:41 CET
           Universal time: jeu. 2022-11-24 15:15:41 UTC
                 RTC time: jeu. 2022-11-24 15:15:42
                Time zone: Europe/Paris (CET, +0100)
    System clock synchronized: yes
              NTP service: inactive
          RTC in local TZ: no

    ## Ci-dessous ntp service est à "inactive"
    ## Pour activer l'utilisation du ntp, on utilise la commande suivante

    user@vm:~$ sudo timedatectl set-ntp true
    user@vm:~$ timedatectl
    Local time: jeu. 2022-11-24 16:15:41 CET
           Universal time: jeu. 2022-11-24 15:15:41 UTC
                 RTC time: jeu. 2022-11-24 15:15:42
                Time zone: Europe/Paris (CET, +0100)
    System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no

    ## Pour que toutes nos modifications prennent effet, on redémarre
    ## le service

    user@vm:~$ sudo systemctl restart systemd-timesyncd

    # Si cela ne fonctionne pas, alors essayer :

    user@vm:~$ sudo systemctl stop systemd-timesyncd
    user@vm:~$ sudo systemctl enable systemd-timesyncd
    user@vm:~$ sudo systemctl start systemd-timesyncd

    ## Et revérifier avec :

    user@vm:~$ timedatectl

```
