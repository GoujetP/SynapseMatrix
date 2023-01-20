# SAE S3.03

- [SAE S3.03](#sae-s303)
  - [Présentation](#présentation)
  - [Procédure générale du projet](#procédure-générale-du-projet)
    - [Installation](#installation)
    - [Machine matrix](#machine-matrix)
    - [Machine db](#machine-db)
    - [Machine element](#machine-element)
    - [Machine rproxy](#machine-rproxy)
  - [Connexion à distance](#connexion-à-distance)
  - [En conclusion](#en-conclusion)

> Remplir ce README avec vos nom, prénom et groupe
> GOUJET Pierre
> Selmène Choukhi

## Présentation

Dans ce projet, notre mission à été de déployer un chat en ligne.

Ce chat est fédéré par [matrix](https://matrix.org), le serveur de</br>
discussion est matrix synapse et le client est ElementWeb.

Pour faire fonctionner le tout, nous avons utiliser nos compétences</br>
en réseau, en système, et notre capacité à trouver des solution seul sur le web.

Nous avons créer un petit réseau privé contenant plusieurs machines communcant
entre elles. Chacune a sa fonction :

- serveur de base de données postgresql
- serveur de discussion matrix
- client web ElementWeb
- reverse proxy
- machine connecté au reste du monde

## Procédure générale du projet

Nous allons ici, fournir tous les liens pour comprendre ce projet et peut-être être</br>
capable de le refaire.

### Installation

Pour travailler sur ce projet, il nous faut des machines.

Voici la procédure pour [créer une machine](Creation_d_une_machine_virtuelle.md), et voilà celle concernant [sa configuration.](Manipulation_d_une_machine_virtuelle.md)

Créer 4 machines 'db', 'rproxy', 'matrix', 'element'.

### Machine matrix

Cette machine va heberger le serveur de discussion synapse.

Pour connaitre les détails sur son installation et sa configuration, rendez-vous</br>
sur cette [procédure](Installation_de_Synapse.md)

### Machine db

'db' pour database, cette machine nous sert à heberger la base de données utilisée</br>
par synapse.

Pour l'installation de cette base, la procédure [installation_Configuration_BDD](installation_Configuration_BDD.md) a été rédigée.

### Machine element

Serveur HTTP, cette machine héberge le client web Element Web.

Plus de détails sur sa mise en place su cette procédure :</br>
[Installation_et_configuration_de_Element_Web](Installation_et_configuration_de_Element_Web.md)

### Machine rproxy

Pièce maitresse dans la sécurité de l'application déployée, cette machine</br>
sert de barrage contre les connexions dangeureuses aux informations des consommateur</br>
stockées sur nos machines.

## Connexion à distance

Le pilier centrale de notre projet est la [connexion SSH](Connexion_a_distance.md) dont on à appris beaucoup
grâce à ce déploiment.

## En conclusion

On a appris ce qui se passait derrière nos développement d'applications.
Cela nous a aidé à prnedre consciences des enjeux derrière un code propre,
léger et peu gourmant.
