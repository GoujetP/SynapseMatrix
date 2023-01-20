# Journal 01

- [Journal 01](#journal-01)
  - [Informations sur le réseau](#informations-sur-le-réseau)
  - [Configurer et mettre à jour la machine virtuelle](#configurer-et-mettre-à-jour-la-machine-virtuelle)
    - [Connexion root et SSH](#connexion-root-et-ssh)
  - [Configurer et mettre à jour la machine virtuelle](#configurer-et-mettre-à-jour-la-machine-virtuelle-1)
    - [Accès extérieur pour les VM](#accès-extérieur-pour-les-vm)
    - [Mise à jour](#mise-à-jour)
    - [Installation d’outils](#installation-doutils)
  - [Quelques trucs en plus](#quelques-trucs-en-plus)

## Informations sur le réseau

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

## Configurer et mettre à jour la machine virtuelle

### Connexion root et SSH

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

## Configurer et mettre à jour la machine virtuelle

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

### Mise à jour

La partie réseau étant faite et le proxy configuré, on peux maintenant mettre à jour la machine qui est pour l'instant sur la version de l'image disque utilisée lors de l'installation.

Pour cela c'est assez simple :

```bash
    user@vm:~$ su -
    Password:
    root@:~\# apt update && apt full-upgrade
```

Si Grub apparait et vous demande de choisir un disque alors cochez /dev/sda à l'aide de la barre d'espacement.

Enfin entrez la commande `reboot` en **root** pour redémarrer et appliquer les changements.

### Installation d’outils

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
