# Journal 01 : La première séance mise en place

## Connection à distance

1. Connexion simple

```bash
    login@phys$ ssh machine.iutinfo.fr
```

Mon login : selmene.choukhi.etu

Ma machine phys : acajou15

*Mes Clés SSH :*

    3072	SHA256:4HPAhIez0aYEBWj1ITUIN7fJakDZfePAtiHEou6CYPU	acajou15.iutinfo.fr	(RSA)

    256	SHA256:eassaZWS5biUXqaJcmIHG/HFImP5NYbMZM88MpVdxfI	acajou15.iutinfo.fr	(ECDSA)

    256	SHA256:+VUt66QJcJzunBlap6geOwgJKnprZtii6ha+2Tq9yGg	acajou15.iutinfo.fr	(ED25519)

2. Connexion rapide via SSH

   2. a. Fabriqurer une paire de clés

*Exemple de création :*

```bash
    login@phys$ ssh-keygen
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/infoetu/login/.ssh/id_rsa):
    Created directory '/home/infoetu/login/.ssh'.
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in /home/infoetu/login/.ssh/id_rsa
    Your public key has been saved in /home/infoetu/login/.ssh/id_rsa.pub
    The key fingerprint is:
    SHA256:fMB2KbRIhY0++6x3VcQ8Zmzz9taiJdE79RREr7oPlLw login@phys
    The key's randomart image is:
    +---[RSA 3072]----+
    |      .=o    +oo |
    |     .o+.. .  @..|
    |     .. * o  * +o|
    |      oo + ...o.=|
    |       oS . +o.=+|
    |      .  . .oo= =|
    |       o   .E+ + |
    |        + . .o   |
    |      .o .  ...  |
    +----[SHA256]-----+
```

   2. b. Transmettre la clé publique au serveur

Pour cela, on peut le faire de deux manière :

- Manuellement
- en utilisant la commande `ssh-copy-id`

On choisi la deuxième méthode :

```bash
    # Pré-requis : avoir défini une clé privée sur la machine client

    # Se connecter au serveur ssh
    login@phys$ ssh virtu.iutinfo.fr

    # Une fois connecté on se rend dans .ssh
    login@virtu$ cd .ssh 
    
    # On se sert ensuite de la commande ssh-copy-id pour copier notre clé public
    login@virtu$  ssh-copy-id -i id_rsa.pub selmene.choukhi.etu@acajou15
```

Une fois exécutée, on est `déconnecté` et à `la prochaine connexion` on entre **`une seule fois`** notre `passphrase` pour accéder à notre `clé privée.` La passphrase est tout de même à remettre dès lors que la clé privée n'est plus en cache.

## Gérer les machines virtuelles

Nous est donnés un script nommé **`vmiut`** permettant d'`utiliser les commandes` de **`virtualbox`** de manière `simplifiée.`

**1. Utilisation de vmiut**

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

utiliser une console virtuelle: simule un clavier et un écran qui serait connectés physiquement à la machine virtuelle
se connecter en SSH.
5.6.1 Console virtuelle
Pour utiliser la console virtuelle, il faut lancer la commande

$ vmiut console matrix
Vous aurez le message d’erreur suivant:

ERROR: Failed to open display:
Ceci est du au fait que la console virtuelle est une application graphique mais que vous êtes connectés à distance sur la machine de virtualisation. L’application graphique ne peut donc pas afficher sa fenêtre.

Pour palier à ce problème, nous allons utiliser une fonctionnalité de SSH qui permet de rediriger une application graphique par la connexion SSH. Pour cela, déconnectez vous de la machine de virtualisation et reconnectez vous avec la commande suivante (en remplaçant virtu par le nom de la machine de virtualisation) :

```bash
    login@phys:~$ ssh -X virtu
```

l’option -X de SSH permet d’effectuer la redirection graphique. Vous pouvez maintenant retenter la commande vmiut console matrix.

Connectez vous en tant que root et utilisez les commandes ip addr show et ip route show pour constater que les paramètres de réseau correspondent bien à ce qui est attendu.


**2. Création/Lancement de la première vm "matrix"**

```bash
    #Pour créer une machine on entre la commande suivante
    login@virtu:~$ vmiut creer matrix

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

    # Pour la lister les machines il suffit de
    login@virtu:~$ vmiut lister
    "Win10" {dc87e177-eb41-457c-9fcc-84c0ca9f38ca}
    "matrix" {903447fe-d6ca-4f2f-9272-3f465a026540}

```

Si, au cours de la SAÉ, le résultat de la commande vmiut lister ressemble à

"<inaccessible\>" {903447fe-d6ca-4f2f-9272-3f465a026540}

alors soit:

- On ne se trouve pas sur la machine de virtualisation;
- les fichiers de notre machine virtuelle ont été effacés.

```bash
    # Pour lancer la machine virtuelle matrix
    login@virtu:~$ vmiut demarrer matrix
    Waiting for VM "matrix" to power on...
    VM "matrix" has been successfully started.

    # Pour arrêter et supprimer la machine virtuelle:
    login@virtu:~$ vmiut arreter matrix

    #puis
    login@virtu:~$ vmiut supprimer matrix
```

Enfin quelques informations importantes quant à nos vm

```bash
    # Une fois la VM démarrée, vous pouvez obtenir des informations sur elle en utilisant :
    login@virtu:~$ vmiut info

    # Dans les informations affichées, vous devriez observer la ligne suivante :

    ip-possible=
    
    # ou

    ip-possible=192.168.194.xx

    # Dans le premier cas, la VM est démarrée mais n’a pas encore obtenu d’adresse IP, il faudra attendre un peu avant de relancer la commande vmiut info et obtenir une ligne correspondante au deuxième cas (xx sera remplacé par un nombre).
```

**3. Informations sur le réseau**

Notre `machine virtuelle` a obtenu une `adresse IP` dans le **`réseau 192.168.194.0/24`**. Il s’agit du **`réseau virtuel principal`**. Dans ce réseau, on a :

| Machine | Adresse |
|---------|---------|
Machine de virtualisation | 192.168.194.1
Routeur, DNS | 192.168.194.2
Adresses dynamiques (attribuées automatiquement) | 192.168.194.25-192.168.194.128

La `machine virtuelle` a été `créée à partir d’un modèle`. Voici les **`caractéristiques du modèle`** :

- *Distribution*: Debian GNU/Linux 11 (bullseye)
- *Utilisateur* standard: user, mot de passe: user
- *Administrateur*: root, mot de passe: root
- *empreinte de la clé SSH :* SHA256:SUHhxVJVZFiBQ6/koNbZfA9reKHyzIrvPgJvOEJ8zuE

**4. Uitlisation de la VM**

Pour utiliser vos machines virtuelles, deux solutions:

- utiliser une console virtuelle: simule un clavier et un écran qui serait connectés physiquement à la machine virtuelle

- se connecter en SSH.

    **a. Console virtuelle**

```bash
    # Pour utiliser la console virtuelle, il faut lancer la commande
    login@virtu:~$ vmiut console matrix

    # Vous aurez le message d’erreur suivant:

    ERROR: Failed to open display:
```

Ceci est du au fait que la **`console virtuelle`** est une **`application graphique`** mais que `vous êtes connectés` **` à distance`**` sur la machine de virtualisation`. L’application `graphique ne peut` donc pas `afficher sa fenêtre.`

Pour palier à ce problème, nous allons utiliser une fonctionnalité de SSH qui permet de rediriger une application graphique par la connexion SSH. Pour cela, déconnectez vous de la machine de virtualisation et reconnectez vous avec la commande suivante (en remplaçant virtu par le nom de la machine de virtualisation) :

```bash
    login@phys:~$ ssh -X virtu

    # l’option -X de SSH permet d’effectuer la redirection graphique. Vous pouvez maintenant retenter la commande vmiut console matrix.
```

Connectez vous en tant que root et utilisez les commandes ip addr show et ip route show pour constater que les paramètres de réseau correspondent bien à ce qui est attendu.

## Configurer et mettre à jour la machine virtuelle

1. **Connexion root et SSH**

Quelle commande avez vous utilisée ?

```bash
    login@virtu:~$ root@192.168.194.3
```

Que se passe-t’il ?

On me demande de vérifier la clé ssh puis on me demande la passwrd de root. Pourquoi ?

Par sécurité interdit de devenir root ?

Quelle est la signification de l’option --login ?

elle sert à se connecter sur une autre session depuis la session courante.

Pourquoi est-il intéressant de l’utiliser ?

car on peut passer par user pour se connecter en ssh et finir par etre root avec :

```bash
    user@vm:~$ su -
    Password:
    root@vm:~#
```

Cela assure plus de sécurité lors de la connection distante.

2. **Accès extérieur pour les VM**

Pour avoir accès à l'extérieur du réseau de l'université on doit passer par le proxy `cache.univ-lille.fr.`
    Il nous faut donc configurer la machine pour quelle passe par celui-ci.

Sachant que la plupart des logiciels en ligne de commande nécessitant l’utilisation d’un proxy peuvent obtenir la configuration de celui ci en regardant le contenu des variables d’environnement, on doit juste initiaiser ces variables.

```bash
    root@vm:~$ nano /etc/environment

    # Dedabs, on écrit :
    HTTP_PROXY=http://cache.univ-lille.fr:3128
    HTTPS_PROXY=http://cache.univ-lille.fr:3128
    http_proxy=http://cache.univ-lille.fr:3128
    https_proxy=http://cache.univ-lille.fr:3128
    NO_PROXY=localhost,192.168.194.0/24,172.18.48.0/22

```

La vérification se fait par l'éxecution de la commande `wget` suivi d'une URL de votre choix. Mais pensez à termner votre connexion ssh et la relancer pour mettre à jour les modification des variables.

3. **Mise à jour**

La partie réseau étant faite et le proxy configuré, on peux maintenant mettre à jour la machine qui est pour l'instant sur la version de l'image disque utilisée lors de l'installation.

Pour cela c'est assez simple :

```bash
    user@vm:~$ su -
    Password:
    root@:~# apt update && apt full-upgrade
```

Si Grub apparait et vous demande de choisir un disque alors cochez /dev/sda à l'aide de la barre d'espacement.

Enfin entrez la commande `reboot` en **root** pour redémarrer et appliquer les changements.

4. **Installation d’outils**

Pour installer une application ou un outils sur debian, on passe par `apt(Advanced Package Tool).` la commande se nomme apt et s'utilise comme suit :

```bash
    user@vm:~$ su -
    Password:
    root@vm:~# apt install vim less tree rsync
```

Ce qui installera les outils {vim, less, tree, rsync}.

## Quelques trucs en plus

L'utilisation de ssh est assez longue et taper ssh virt, puis ssh vm peut être pénible lors du déploiement de l'application.

Pour corriger cela o va passer par le fichier .ssh/config pour parametrer nos connections à la machine de virt et la vm.

```bash
    user@vm:~$ nano .ssh/config

    # À l'intérieur on y ajoute :

    Host *
        ForwardAgent yes
        ForwardX11 yes

    Host virt
        HostName {virt}.iutinfo.fr
        User {prenom.nom.etu}

    Host vm
        HostName 192.168.194.3
        User user
        ProxyJump virt

```

Chaque bloc correspond à une machine, le premier bloc s'adresse à tous les autres avec le caractère `*` désignant l'ensemble des machines.

Ensuite le bloc `virt` fait référence à notre machine de virtualisation dont l'adresse est {virt}.iutinfo.fr.

Et `vm` représente notre machine virtuelle. On peut voir qu'elle possède un `ProxyJump` qui signifie qu'on doit passer par une connection ssh sur virt avant de se connecter à vm.

_*Remarque :*_

Par défaut, la connection ssh se fait avec le login de la session courante. On ajoute le paramètre `User` pour spécifier le login à utiliser comme ci-dessus.
