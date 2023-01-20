
# Création d'une machine virtuelle

- [Création d'une machine virtuelle](#création-dune-machine-virtuelle)
  - [Création/Lancement de la première vm "matrix"](#créationlancement-de-la-première-vm-matrix)
    - [Création d'une machine avec vmiut](#création-dune-machine-avec-vmiut)
    - [Lancement d'une machine avec vmiut](#lancement-dune-machine-avec-vmiut)
    - [Informations importantes](#informations-importantes)
    - [Accès extérieur pour les VM](#accès-extérieur-pour-les-vm)

## Création/Lancement de la première vm "matrix"

Pour manipuler des machines virtualbox en ligne de commandes, on</br>
utilisera un script nommé **`vmiut`** permettant d'`utiliser les commandes`</br>
de **`virtualbox`** de manière `simplifiée.`

Pour comprendre son utilisation référez-vous à [la partie 1 de Gérer une machine virtuelle](Journal01.md#gérer-les-machines-virtuelles)

_*Note :*_ Nous nommerons notre machine virtuelle matrix pour le</br>
reste de la procédure.

### Création d'une machine avec vmiut

```bash
    ## Pour créer une machine avec vmiut on entre la commande suivante
    login@virtu:~$ vmiut creer matrix

    ## On obtient en retour quelque chose de similaire à : 
        Virtual machine 'matrix' is created and registered.
        UUID: 903447fe-d6ca-4f2f-9272-3f465a026540
        Settings file: '/usr/local/virtual_machine/infoetu/login/matrix/matrix.vbox'
        0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
        Clone medium created in format 'VMDK'. UUID: 99d2712d-e40d-4661-995f-b1147131414b
        # Paramètres vmiut
        MACHINE=matrix
        VBOXES=/usr/local/virtual_machine/infoetu/login
        RESEAU=vmnet8
        MEMOIRE=1024
        VRDEPORT=5000-5050
        MODELE=/home/public/vm/disque-5Go-bullseye.vdi

        # Paramètres VirtualBox
        name=matrix
        UUID=903447fe-d6ca-4f2f-9272-3f465a026540
        path=/usr/local/virtual_machine/infoetu/login/matrix
        memory=1024
        etat=poweroff
        vrdeport=-1
        mac=08:00:27:ba:9a:8d

    ## Pour la lister les machines il suffit d'entrer la commande suivante :
    login@virtu:~$ vmiut lister

    ## Le retour ressemblant à :
    "Win10" {dc87e177-eb41-457c-9fcc-84c0ca9f38ca}
    "matrix" {903447fe-d6ca-4f2f-9272-3f465a026540}

```

NB : Si, au cours de la SAÉ, le résultat de la commande vmiut lister ressemble à

"<inaccessible\>" {903447fe-d6ca-4f2f-9272-3f465a026540}

alors soit:

- On ne se trouve pas sur la machine de virtualisation;
- les fichiers de notre machine virtuelle ont été effacés.

### Lancement d'une machine avec vmiut

```bash

    ## Pour lancer la machine avec vmiut virtuelle matrix
    login@virtu:~$ vmiut demarrer matrix

    ## Le retour semblable à :
        Waiting for VM "matrix" to power on...
        VM "matrix" has been successfully started.

    ## Pour arrêter et supprimer la machine virtuelle :
    login@virtu:~$ vmiut arreter matrixs

    ## Puis :
    login@virtu:~$ vmiut supprimer matrix
```

### Informations importantes

Enfin quelques informations importantes quant à nos vm.

```bash
    
    ## Une fois la VM démarrée, vous pouvez obtenir des informations sur elle en utilisant :
    login@virtu:~$ vmiut info

```

Dans les informations affichées, vous devriez observer la ligne suivante `'ip-possible='` <br/> ou `'ip-possible=192.168.194.XX'`

Dans le premier cas, la VM est démarrée mais n’a pas encore obtenu d’adresse IP, il faudra attendre un peu avant de relancer la commande vmiut info et obtenir une ligne correspondante au deuxième cas (xx sera remplacé par un nombre).

### Accès extérieur pour les VM

Pour avoir accès à l'extérieur du réseau de l'université on doit passer par le proxy `cache.univ-lille.fr.`
    Il nous faut donc configurer la machine pour quelle passe par celui-ci.

Sachant que la plupart des logiciels en ligne de commande nécessitant l’utilisation d’un proxy peuvent obtenir la configuration de celui ci en regardant le contenu des variables d’environnement, on doit juste initiaiser ces variables.

```bash
    root@vm:~$ nano /etc/environment

    # Dedans, on écrit :
    HTTP_PROXY=http://cache.univ-lille.fr:3128
    HTTPS_PROXY=http://cache.univ-lille.fr:3128
    http_proxy=http://cache.univ-lille.fr:3128
    https_proxy=http://cache.univ-lille.fr:3128
    NO_PROXY=localhost,192.168.194.0/24,172.18.48.0/22

```

La vérification se fait par l'éxecution de la commande `wget` suivi d'une URL de votre choix. Mais pensez à terminer votre connexion ssh et la relancer pour mettre à jour les modification des variables.