# Connection à distance

- [Connection à distance](#connection-à-distance)
  - [Connexion simple](#connexion-simple)
  - [Connexion rapide via SSH](#connexion-rapide-via-ssh)
    - [Fabriqurer une paire de clés](#fabriqurer-une-paire-de-clés)
    - [Transmettre la clé publique au serveur](#transmettre-la-clé-publique-au-serveur)
  - [Pour aller plus vite encore](#pour-aller-plus-vite-encore)

## Connexion simple

Pour se connecter à une machine distante, on passe par la commande ssh.

Son utilisation requiert plusieurs choses :

- Être sur le même réseau que la machine distante.
- Avoir un couple (identifiant, mot de passe) de session sur la machine distante.

On utilisera pour la suite le nom 'dist' pour la machine distante et</br>
('user','psswd') pour le couple (identifiant, mot de passe). Le nom</br>
'phys' correspond à la machine physique devant laquelle on se trouve</br>
et la session dessus est nommée 'login'.

La commande ssh s'utilise donc comme suit :

```bash

    ## La commande
    login@phys:~$ ssh user@dist

    ## En retour, le mot de passe de 'user' est demandé
    user@dist\'s password:

    ## Nous somme desormais connecté à dist sur la session user
    user@dist:~$ 

```

Toute les commandes entrée par la suite sont traitées par la machine</br>
dist. Pour se déconnecter il suffit de sortir de l'intance de terminal</br>
en tapant la commande 'exit'.

## Connexion rapide via SSH

Lorsqu'on se connecte à distance à une machine pour la configurer,</br>
on risque de se reconnecter souvent. Dans ces cas là, il y a une solution</br>
pour ne pas entrer son mot de passe à chaque fois. On utilise la</br>
[cryptographie asymétrique (ou à clé public)](https://fr.wikipedia.org/wiki/Cryptographie_asym%C3%A9trique) plus sécurisé qu'un mot</br>
de passe.

Le principe d’utilisation pour l’authentification SSH est le suivant :</br>
on au serveur SSH notre clé publique et, au moment de la connexion,</br>
un challenge cryptographique permettra au serveur de vérifier qu'on</br>
possède bien la clé privée associée à la clé publique sans que notre</br>
clé privée ne soit jamais diffusée en dehors de notre machine physique.

### Fabriqurer une paire de clés

*Voici un exemple de création :*

```bash

    ## La commande pour générer une paire de clé
    login@phys:~$ ssh-keygen

    ## Le retour étant :
        Generating public/private rsa key pair.
        Enter file in which to save the key (/home/infoetu/login/.ssh/id_rsa):
        Created directory '/home/infoetu/login/.ssh'.
    ## Ici, on nous demande une passphrase qui va venir sécurisé la copie de notre clé
    ## Vers une machine, même notre session est ouverte.
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

### Transmettre la clé publique au serveur

Pour cela, on peut le faire de deux manière :

- Manuellement
- en utilisant la commande `ssh-copy-id`

On choisi la deuxième méthode :

```bash
    
    ## Pré-requis : avoir défini une clé privée sur la machine client
    
    ## On se sert de la commande ssh-copy-id pour copier notre clé public
    ## L'option -i permet de donner le chemin vers notre clé.
    ## On donne ensuite l'adresse de la machine vers laquelle transmettre la clé
    login@phys:~$ ssh-copy-id -i .ssh/id_rsa.pub dist

```

Une fois exécutée, on entre **`une seule fois`** notre `passphrase`</br>
pour accéder à notre `clé privée.` La passphrase est tout de même à</br>
remettre dès lors que la clé privée n'est plus en cache(lors du</br>
redémarrage de votre machine par exemple).

## Pour aller plus vite encore

Si vous devez vou connecter en ssh plusieurs fois de suite l'utilisation</br>
de ssh est assez longue et taper 'ssh premiereMachine' puis</br>
'ssh secondeMachine' peut être pénible lors du déploiement de</br>
l'application.

Pour corriger cela on va passer par le fichier .ssh/config pour parametrer</br>
nos connexions à la premiereMachine et la secondeMachine.

```bash
    login@phys:~$ nano .ssh/config

    ## À l'intérieur on y ajoute :

    Host *
        ForwardAgent yes
        ForwardX11 yes

    Host premiereMachine
        HostName premiereMachine
        User userPremiereMachine

    Host secondeMachine
        HostName secondeMachine
        User userSecondeMachine
        ProxyJump premiereMachine

```

Chaque bloc correspond à une machine, le premier bloc s'adresse à tous</br>
les autres avec le caractère `*` désignant l'ensemble des machines.</br>

Ensuite le bloc `premiereMachine` fait référence à notre premiere</br>
connexion ssh, celle que l'on voit sur notre réseau et qu'on peut</br>
atteindre directement.

Et `secondeMachine` représente notre machine accessible sur</br>
`premiereMachine` uniquement. On peut voir qu'elle possède un</br>
`ProxyJump` qui signifie qu'on doit passer par une connection ssh</br>
sur `premiereMachine` avant de se connecter à secondeMachine.

_*Remarque :*_

Par défaut, la connection ssh se fait avec le login de la session</br>
courante. On ajoute le paramètre `User` pour spécifier le login à</br>
utiliser comme ci-dessus.
