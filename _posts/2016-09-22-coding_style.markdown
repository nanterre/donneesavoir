---
layout: post
title:  "Coding Style"
date:   2016-09-22
categories: doc
tags: doc
---

## Conventions GIT

De manière générale, nous sommes en accord avec tout ce qui est dit dans ce [billet de blog à propos des conventions GIT] (https://medium.com/code-adventures/a940ee20862d) et il devrait en être de même pour vous. Nous ajoutons/modifions uniquement les lignes suivantes :

Les commits doivent être les plus simples (atomiques) possibles, mais pas plus simples. Cela signifie que vous devez toujours pouvoir annuler des `fix`es, `edit`s, `update`s, `add`s, et `remove`s spécifiques avec `git revert`

## Javascript

* Deux espaces pour l'indentation, jamais de tabulation.
* `var` pour tout, jamais de virgule en premier.
* Des guillemets simples uniquement, jamais de guillemets doubles (à l'opposé du HTML/Jade).
* Éviter les espaces en fin de ligne. Les lignes vides ne doivent pas contenir d'espace.
* La documentation est en ligne pour les nouvelles méthodes, les nouveaux membres de classe, etc.
* Un espace entre les fonctions/conditions et leurs parenthèses et leurs accolades.
  * `if (..) {`
  * `for (..) {`
  * `while (..) {`
  * `function (err) {`
* Utiliser les [conditions Yoda](https://en.wikipedia.org/wiki/Yoda_conditions), toujours `'string' === typeof el` au lieu de `typeof el === 'string'`.

## Feuilles de style

* Voir le fichier [.stylintrc](/.stylintrc). Plus d'informations sur : https://rosspatton.github.io/stylint/
* Pour les styles de modules spécifiques, se conformer à `#unique-template-top-node-selector .my-generic-css-update { ... }`
* `.classes` et `#ids` doivent uniquement être utilisés pour faire référence aux fichiers `CSS`. Si vous souhaitez utiliser des éléments de `JS` utilisez `data-*` afin d'être sure d'où l'élément est référencé.
* Pour les multiples sélecteurs séparés par des virgules, placez chaque sélecteur sur sa propre ligne.
* Les sélecteurs d'attribut, comme `input[type="text"]` doivent toujours entourer la valeur de l'attribut entre des guillemets doubles, pour des raisons de cohérence et de sécurité (voir ce [billet de blog sur les valeurs d'attributs non ponctuées](http://mathiasbynens.be/notes/unquoted-attribute-values) qui peuvent conduire à des attaques XSS).

## HTML/Jade

* Deux espaces pour l'indentation, jamais de tabulations.
* Uniquement des guillemets doubles, jamais de guillemet simple.
* Éviter les espaces en fin de ligne. Les lignes vides ne doivent pas contenir d'espace.
* Utiliser CDNs et HTTPS pour les parties JS tierces lorsque possible. Nous n'utilisons d'URLs protocole-relatives dans ce cas parce qu'elles se cassent lors de l'affichage de la page localement via `file://.

## Modules locaux

* Chaque module doit être placé dans son dossier conteneur dans `/lib`, comme [NodeJS Starter](https://github.com/rickyrauch/nodejs-starter). _(e.g. `/lib/sidebar`, `/lib/models`)_
* Points d'entrée :
  + **Server side JS:** `index.js`. _(e.g. `/lib/db-api/index.js`)_
  + **Client side JS:** `${module-name}.js`. _(e.g. `/lib/sidebar/sidebar.js`)_
  + **Client side CSS:** `styles.styl`. _(e.g. `/lib/sidebar/styles.styl`)_
  + Le fichier de modèle principal `HTML` est `template.jade`. _(e.g. `/lib/sidebar/template.jade`