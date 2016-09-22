---
layout: post
title:  "Guide de développement sous DemocracyOS"
date:   2016-09-22
categories: doc
tags: doc
---

Nous allons utiliser la version 1.0.0 de DemocracyOS.

# Stack

#### Node.js et NPM
Vous avez besoin de [Node.js v4.0.0](https://nodejs.org/en/blog/release/v4.0.0/) ou supérieure pour exécuter DemocracyOS.

Il y a plusieurs options disponibles pour l'installer :

* [Site officiel](https://nodejs.org/en/download/): aussi bien Node.js que NPM sont intégrés à cette installation.

* [NVM](https://github.com/creationix/nvm): Node Version Manager - Script bash simple pour gérer de multiples versions actives de node.js

* [tj/n](https://github.com/tj/n): un autre -en fait plus simple- gestionnaire de versions node


#### MongoDB

Nous prenons aussi en charge la [dernière version](http://blog.mongodb.org/post/130823293808/mongodb-319-is-released) de [MongoDB](https://www.mongodb.org/).

Donc [téléchargez](https://www.mongodb.org/downloads) la version correspondant à votre système et suivez les instructions d'installation.

```
Remarque : nous envisageons d'utiliser Docker à la fois pour le développement et la production afin que commencer à développer sur une nouvelle machine soit très facile et de pouvoir exécuter l'application dans le même environnement qu'en production.

```

#### Browserify

Nous utilisons [Browserify](http://browserify.org) pour lier le code côté client, mais nous l'utilisons comme une dépendance donc vous n'avez pas besoin d'installer quoi que ce soit.

#### Système d'exploitation

Actuellement nous ne prenons pas Windows en charge, donc si vous l'utilisez, vous devez installer VirtualBox avec une distribution Linux. Nous préférons Ubuntu parce que nous savons que DemocracyOS s'exécute dessus.


# Configuration

Toutes les variables de configuration sont dans le fichier `/config/defaults.json`. Si vous souhaitez redéfinir l'une d'entre elles, vous devez créer un nouveau fichier appelé `development.json` dans le même répertoire et réécrire le nom et la valeur que vous voulez.

Également, pour passer les variables de configuration au client, nous utilisons la clé `client` qui accepte une matrice des autres noms de variables de configuration. 

Donc si vous souhaitez ajouter une variable appelée `exemple` et la passer au client, les étapes sont :

1. Ajouter la paire de valeurs de clé au fichier `defaults.json`, la clé doit être `exemple` et la valeur ce que vous voulez. Elle doit avoir une valeur par défaut, même si c'est une chaîne vide.
2. Si vous voulez passer cette variable au client, ajoutez alors `'exemple'` à la matrice cliente dans le même fichier.
3. Si vous voulez tester une valeur différente avec la variable `exemple`, créez une fichier `development.json` dans le même répertoire et écrasez la valeur par défaut.

Les noms des variables de configuration doivent être `camelCased` puisqu'en production, nous faisons correspondre les Variables d'Environnement avec les Variables de Configuration de cette façon :

Dans defaults.json:
```
{
  ...
  maVariable: 'par_defaut',
  monAutreVariable: {
    unePropriété: 10
  }
  ...

}
```
En production, ces variables d'environnement doivent être:

```
Ma_VARIABLE="pas par défaut"
Mon_AUTRE_VARIABLE_UNE_PROPRIETE=20
```
Comme vous le voyez nous faisons correspondre camelCase et les objets imbriqués avec des tirets de soulignement et nous déduisons le type en utilisant le fichier `defaults.json`  (`maVariable` est une `chaîne` et `unePropriété` est un nombre dans ce cas).

# Exécuter l'application

DemocracyOS peut être exécuté de différentes manières, mais nous préférons juste utiliser `make` dans le dossier root.

Une autre façon est d'exécuter `gulp bws` pour le développement, cela attendra des modification dans les fichiers projet pour recompiler à chaque fois. Pour cela nous devons avoir `gulp` installé :

```
npm install -g gulp
```

# Modèles

Toutes nos données sont stockées dans MongoDB et pour s'y connecter, nous utilisons [mongoose](http://mongoosejs.com/), un modelage d'objet mongodb élégant pour node.js.

Les modèles sont stockés dans le répertoire `lib/models`. Si vous souhaitez ajouter un nouveau modèle, alors faites-le dans un nouveau fichier dans ce répertoire en suivant une structure similaire aux modèles que nous avons. 

Après cela `appelez` le dans `lib/models/index.js`.

# Database-API

Pour créer, lire, mettre à jour et supprimer les modèles, nous avons une couche de données qui interagit avec les modèles. Elle est appelée `db-api`
et est située sous `lib/db-api`.

Après avoir créé un nouveau modèle, vous devez inclure un nouveau fichier dans le répertoire `lib/db-api` avec son nom et et des méthodes d'`export` pour :

* create
* search
* all
* findOne
* destroy
* update
* custom operation

Vous pouvez voir des exemples dans ce répertoire pour voir les `db-api`s que nous avons actuellement.

# Web API

Nous exposons ces appels de l'api de la base de données via une api restful. Chaque `modèle` a un fichier différent qui suit la convention `lib/${modelName}-api/index.js`, par exemple : `lib/topic-api/index.js`.

Ces modules sont ajoutés à l'application dans `lib/boot/index.js` et vous devez les ajouter comme :

```
app.use('/api', require('lib/${modelName}-api'));
```
De façon à ce qu'il puisse être accédé sur le client par l'URL : `/api/modelName`.

# Routing

### Côté serveur

Si vous souhaitez ajouter une nouvelle route à DemocracyOS, la première chose à faire est de lui créer un nouveau module sous `lib` et d'exposer une `app` (une instance `express`) qui nécessite `lib/layout`. Par exemple :

Dans `lib/mypage/index.js`
```
/**
 * Module dependencies.
 */

var express = require('express');
var app = module.exports = express();

app.get('/mypage', require('lib/layout'));
```

Et à la fin de `lib/boot/index.js`
```
app.use(require('lib/mypage'));
```

### Côté client

Pour le routage côté client, nous utilisons [page.js](https://github.com/visionmedia/page.js), 

un micro routeur côté client inspiré par le routeu Express.

Vous devez créer un nouveau fichier dans `lib/mypage/mypage.js` et inclure les lignes suivantes :

```
import page from 'page';

page('/mypage', (ctx, next) => {
  // your page logic goes here
});
```

Si vous créez une page pour afficher du contenu (et non des sujets) vous devez d'abord vider et ensuite tout restituer dans l'élément `#content`. 
Vous pouvez faire cela simplement avec [component-dom](https://github.com/component/dom):

```
import o from 'component-dom';
import page from 'page';
import MyPageView from './view';

page('/mypage', (ctx, next) => {
  let myPage = new MyPageView();

  let el = o('#content');
  myPage.appendTo(el[0]);
});
```

Ce qui nous conduit à... affichages.

# Views

Pour créer de nouveaux affichages (le HTML que vous allez afficher pour l'utilisateur final) commencez par créer un nouveau fichier dans `lib/mypage` appelé `view.js`. Ceci si vous avez une route appelée de cette façon, mais si vous n'en avez pas, vous pouvez juste le créer dans `lib/my-view/view.js`.

Nous avons notre propre bibliothèque `Affichage` dans `lib/view/view.js` qui fournit des méthodes pour :

* `constructor`: passe un modèle et les locales pour ce modèle
* `switchOn`: fonction appelée que les modèle est restitué dans le DOM
* `switchOff`: fonction appelée que le modèle est supprimé du DOM
* `appendTo`: joint l'élément créé à l'intérieur de l'élément passé
* `bind`: utilisé pour lié des gestionnaires d'événement
* vous pouvez en voir d'autres dans `lib/view/view.js`

Vous ne devriez pas toucher à ce fichier mais plutôt l'étendre avec vos propres affichages :

```
import View from '../view/view.js';
import template from './template.jade';

export default class MyView extends View {

  constructor (topic) {
    super(template, { topic });
  }
}
```

# Modèles

Dans l'exemple précédent, nous avons utilisé un modèle. Ce fichier est écrit dans le format [jade](http://jade-lang.com/) et il ne doit avoir qu'un et un seul élément conteneur. Si vous écrivez quelque chose comme :

```
.topic-container
  .topic-body
    // ...
.comments
  .comment-form
    // ...
```

Vous avez deux conteneurs racine (`topic-container` et `.comments`) et vous aurez des problèmes avec ça. Les modèles peuvent être passés en données dans les objets `locales` : dans l'affichage lorsque vous appelez `super(template, locals)`.

# Stores

Les stores sont responsables de la communication avec l'API côté serveur afin d'obtenir les données nécessaires. Un store doit avoir une fonction `name` définie et renvoyant le nom du modèle qui lui est relatif, parce qu'elle est utilisées pour construire l'URL de l'API. 

Pour faire des requêtes sur le serveur nous utilisons notre propre mode sur `lib/request/request.js` qui utilise [superagent](https://github.com/visionmedia/superagent).

Comme pour les Affichages, si vous créez une nouveau store, vous devez étendre `lib/store/store.js`.

```
import Store from '../store/store';

class MyModelStore extends Store {
  name() {
    return 'myModel'; // calls will be made to '/api/myModel'
  }
}
```

Ce store est déjà lié avec des méthodes domme :

* findOne
* findAll
* destroy

Le store utilises [Promises](http://babeljs.io/docs/learn-es2015/) pour faire des appels asynchrones, attrapant les 'promises' dans une variable privée appelée `_fetches`.

Pour voir un exemple complet d'un store personnalisé, vous pouvez regarder `lib/topic-store/topic-store.js`.

# Middlewares

Vous allez sans doute avoir besoin de données sur vos pages, pour cela vous allez créer une fonction personnalisée [middleware](https://github.com/visionmedia/page.js#routing).

Pour garder le tout organisé, nous avons créé quelques modules comme `lib/topic-middlewares/topic-middlewares.js`.

Sous les routes qui nécessitent des données, vous devez inclure ces middlewares et vous devez également utiliser un store pour faire ces appels.

Quand les données sont prêtes, vous devez alors appeler `next` sur ce middleware, par exemple :

```
export function findMyModel(ctx, next) {
  myModelStore
    .findOne(ctx.params.id)
    .then(model => {
      ctx.model = model;
      next();
    })
    .catch(err => {
      if (404 !== err.status) throw err;
      log(`Unable to load model for ${ctx.params.id}`);
    });
}
```
