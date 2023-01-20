# Installation et configuration de Synapse

- [Installation et configuration de Synapse](#installation-et-configuration-de-synapse)
  - [Accès à un service HTTP sur la VM](#accès-à-un-service-http-sur-la-vm)
    - [Un premier service pour tester](#un-premier-service-pour-tester)
    - [Accès au service depuis la machine physique](#accès-au-service-depuis-la-machine-physique)
  - [Installation de Synapse](#installation-de-synapse)

## Accès à un service HTTP sur la VM

Pour que le service que nous allons installer soit accessible par toutes</br>
les machines de TP de l’IUT, celui-ci doit écouter sur les interfaces</br>
réseaux de notre machine de virtualisation.

Or, nous allons installer nos services dans notre machine virtuelle.</br>
Pour rappel, le réseau virtuel principal, dans lequel se situe notre VM,</br>
n’est accessible que depuis la machine de virtualisation.

### Un premier service pour tester

Sur la machine virtuelle, nous allons installer `ngnix`, un serveur HTTP.
Pour cela, il nous suffit de taper la commande :

```bash
    login@vm:~$ sudo apt update
    login@vm:~$ sudo apt install nginx
```

En cas d'échec du démarrage du service, pensez à désactiver le service
apache2.service avec la commande `systemctl stop apache2.service`. Ensuite,
activer le service nginx avec `systemctl start nginx`.

Maintenant installons `curl`, un client HTTP. Ilpourra donc récupérer les
pages proposées par nginx.

```bash
    login@vm:~$ sudo apt update
    login@vm:~$ sudo apt install curl
```

Sur la vm, la seule commande 'curl http://localhost fonctionne'. En revanche
pour avoir accès à la page http://localhost de la `machine virtuelle` depuis
la machine de vitualisation, il faut ajouter quelques option. Dans notre cas
la commande sera :

```bash
    login@virt:~$ curl -x 192.168.1994.3:80 http://localhost

    # l'option -x étant pour préciser un proxy par lequel
    # passé. Ici, il s'agit de notre vm au port 80.
```

### Accès au service depuis la machine physique

Pour accéder à la page localhost de notre vm depuis la `machine physique`,
c'est impossible. En effet la machine virtuelle est sur un réseau privé de la
machine de virtualisation on ne peux donc pas y accéder sans passer par l'option
-L de ssh.

Lors de la connexion ssh à une machine distante on peut préciser à notre machine
physique un port spécifique de la machine distante à écouter.

```bash
    login@phys:~$ ssh -L 8080:localhost:80 vmjump

    # Où vmjump représente la connexion ssh entre notre
    # machine physique et notre vm en passant par un
    # ProxyJump sur la machine de virtualisation.

    # L'option -L se lit : le port 8080 de notre machine
    # physique écoute à l'adresse localhost:80 de la vm.

```

Pour configurer cette redirection de port de manière persistante il faut se rendre
dans _.ssh/config_ et ajouter à notre connexion à la vm l'option : "LocalForward 8080 localhost:80".
On peut également faire une copie de la configuration de vmjump pour en faire une
nommée par exemple tunnel.

## Installation de Synapse

Pour l'installation de synapse, une procédure à part entière à été rédigée à cette
adresse : [Installation_de_Synapse.](./Installation_de_Synapse.md)
