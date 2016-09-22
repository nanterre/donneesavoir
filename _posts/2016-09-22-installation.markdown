---
layout: post
title:  "Installation"
date:   2016-09-22 14:24:00
categories: doc
tags: doc
---

Ceci est une référence rapide pour avoir DemocracyOS installé et s'exécutant.

* [Prérequis](#prerequis)
  * [Notes](#notes)
* [Installation](#installation)
* [Exécution](#execution)
  * [Exécution dans un environnement de production](#execution-dans-un-environnement-de-production)
  * [Sépcificités OS](#specificites-os)
* [Chargement de données exemple](#chargement-de-donnees-exemple)

## Prérequis

Pour démarrer avec DemocracyOS, vous devez avoir installé :

* [MongoDB](http://www.mongodb.org/downloads) base de données de document open-source.
* [NodeJS & NPM](http://nodejs.org/) plateforme.
* [Git](http://git-scm.com/downloads) système de controle de version distribué. Si vous êtes sur github et n'avez pas `git`, [vous avez faux](http://knowyourmeme.com/memes/youre-doing-it-wrong).
* [Make](http://www.gnu.org/software/make/) utilitaire d'automatisation de la compilation.
* [OpenSSL](https://www.openssl.org/related/binaries.html) pour le cas où vous souhaiteriez générer des certificats SSL.

### Notes

L'exécution dans un environnement **Windows** n'est actuellement pas pris en charge. Si vous connaissez bien [Docker](https://www.docker.io/) ou [Vagrant](http://www.vagrantup.com/), venez nous aider et soumettez un [PR](http://help.github.com/articles/using-pull-requests) de façon à ce que nous puissions collaborer à une solution pour cela.

## Installation

### Unix and OS/X

- [Fork](http://help.github.com/articles/fork-a-repo) ou téléchargez depuis ce dépôt.
- `cd` jusqu'à l'emplacement du projet
- Assurez-vous que MongoDB est en cours d'exécution et peut être atteint comme configuré dans `config/development.json`. (Les valeurs par défaut devraient fonctionner)
- Exécuter `make`
- Boum! DemocracyOS devrait s'exécuter sur le port 3000.

> Veuillez vous référer à la section [Configuration](configuration.md) pour personnaliser les paramètres.


## Exécution

Une fois que les composants et les dépendances DemocracyOS sont installés, vous pouvez démarrer l'application comme ceci :

```bash
make run
```
Regardez dans le [Makefile](https://github.com/DemocracyOS/app/blob/master/Makefile) pour plus d'informations sur les tâches qu'il vous est possible d'exécuter.

Vous pouvez vérifier la version actuelle de DemocracyOS en allant à `http://localhost:3000/api`

### Utilisation de SSL

Dans le cas où vous souhaitez utiliser SSL dans votre environnement de développement (c'est désactivé par défaut) vous avez besoin d'avoir des fichiers de certificats corrects. Nous fournissons un script qui génère les fichiers nécessaires. Exécutez la commande suivant à la racine du projet :

```bash
NODE_PATH=. node bin/dos-ssl
```

Puis modifiez votre fichier de configuration en changeant la propriété `protocol` à `https` et exécutez-le normalement. Les options de configuration pour SSL sont listées [ici](configuration.md#ssl).

### Une note sur l'utilisation du port 443

Le fichier de configuration par défaut fait écouter le port 443 à l'application pour prendre en charge les connexions SSL. Sur certains OS, un utilisateur normale ne peut exécuter cette opération, et vous obtiendrez surement cette erreur :

```javascript
events.js:72
       throw er; // Unhandled 'error' event
             ^
Error: bind EACCES
   at errnoException (net.js:904:11)
   at net.js:1072:30
   at Object.37:1 (cluster.js:594:5)
[...]
```

Pour la résoudre sans être root (ce qui est toujours une mauvaise idée, vous pouvez modifier la valeur `ssl.port` dans le fichier de configuration vers un autre port, disons `4443`.

### Exécution dans un environnement de production

1. Configurez les [variables d'environnement](https://github.com/DemocracyOS/app/wiki/Environment-variables) pour la production; de façon spécifique, définissez `NODE_ENV` sur `production`
2. Définissez l'instance MongoDB pour s'exécuter comme service.
3. Assurez-vous de configurer correctement le [Notifier](configuration.md#embebed-notifier-server) pour les environnements de production.
4. À partir du chemin racine du prjet, vous devez `make`ou :
  1. `npm install` pour installer les dépendances node.
  2. `npm run build && npm run start` pour compiler les ressources et exécuter l'application. _N'exécutez pas comme`sudo`._

Si quelque chose se passe mal, vous pouvez toujours revenir à un slate propre en exécutant `make clean`.

### Spécificités OS

* Lisez [ce guide très détaillé](https://github.com/okfn-brasil/democracyos/wiki/Install) si vous êtes sur Ubuntu 10 LTS.
* Sur Ubuntu 14/13/inférieur, installez le package `node-legacy` pour NodeJS.
* Lisez [ce guide](https://github.com/DemocracyOS/app/wiki/Running-as-a-service) pour savoir comment exécuter DemocracyOS comme service.

## Chargement de données exemple

Pour pouvoir voir un déploiement fonctionnant complètement, vous *allez avoir* besoin de données d'exemple. Cela peut être réalisé par l'une ou l'autre de ces approches :
* Manuellement, [chargez des données exemple](https://github.com/DemocracyOS/app/wiki/Load-fixtures) intégrées à DemocracyOS.
* Définissez et accédez le [module d'administrat](https://github.com/DemocracyOS/app/wiki/Admin-module).
