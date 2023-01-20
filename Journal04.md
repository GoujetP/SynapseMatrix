# Journal 04 : Installation et configuration de Element Web

- [Journal 04 : Installation et configuration de Element Web](#journal-04--installation-et-configuration-de-element-web)
  - [Element Web](#element-web)
  - [**TODO FAIRE UNE JUSTIFICATION SUR LE CHOIX NGINX**](#todo-faire-une-justification-sur-le-choix-nginx)

## Element Web

Pour rendre Element accessible depuis son navigateur,
il faut installer un serveur web permettant de `servir`
Element.

Nous installerons nginx sur la `machine virtuelle`
matrix avec la commande :

```bash
    user@vm:~$ sudo -E apt install nginx
```

## **TODO FAIRE UNE JUSTIFICATION SUR LE CHOIX NGINX**

Par défaut nginx sert sur le port 80 du serveur. Pour
accéder au service nginx depuis la `machine de`
`virtualisation` sur le port 8080, il faut indiquer à
cette machine d'écouter, via ce port, le port 80 de
la vm.

Cela se fait en ajoutant une ligne de configuration
dans le fichier _.ssh/config_ sur la connexion qu'on
avait nommé précédemment tunnel.

Voici le résultat de l'opération :

```txt
    Host tunnel
        HostName 192.168.194.3
        User user
        ProxyJump virt
        LocalForward virt.iutinfo.fr:8008 localhost:8008
        Localforward virt.iutinfo.fr:8080 localhost:80
```

Une fois configuré, on obtient la page d'accueil par
défaut suivante à l'adresse 'http://virt.iutinfo.fr:8080' :

![Accueil nginx](../S3.03/res/WelcomeToNginx.png)

TODO FAIRE UNE PROCÉDURE INSTALLATION ElementWeb.