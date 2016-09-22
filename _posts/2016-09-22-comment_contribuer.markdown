---
layout: post
title:  "Comment contribuer à DemocracyOS"
date:   2016-09-22
categories: doc
tags: doc
---

Si vous avez une question concernant DemocracyOS (qui ne concerne pas un bug), merci de l'envoyer à notre [adresse de contact](contact@democracyos.eu).

##  Signaler des bugs / Demander des fonctionnalités 

Pour des raisons pratiques nous n'acceptons que des issues qui concernent des signalement de bugs ou des demandes d'ajout de fonctionnalités. Assurez-vous de lire les conseils ci-dessous avant d'ouvrir des issues. 

### Eviter les doublons 

Vous nous aidez beaucoup si vous commencez par vérifier que personne n'a déjà signalé le même bug. De plus, le bug a déjà peut-être été corrigé par un correctif disponible. 

### Aidez-nous, aidez-vous

Partagez autant d'information que possible. Indiquez le système d'exploitation et sa version, le navigateur et sa version, la version de DemocracyOS utilisée. Indiquez également les étapes permettant de reproduire le bug, et tous les journaux du navigateurs et/ou processus lorsque c'est possible. 

Une bonne démarche pour signaler un bug repose sur la capacité à le reproduire. Vous pouvez notamment suivre ce modèle : (kudos to [@bastianhell](https://github.com/bastianhell)):

> 1. Exécutez l'application avec `make`
> 2. Naviguez jusqu'à `/route`
> 3. Cliquez sur le `bouton` et voyez l'erreur dans la sortie `console`
>
> Résultats: En fait, quel est le résultat qui en fait un bug ? (e.g : Dans l'en-tête le texte n'est pas aligné, voir la copie d'écran jointe, etc.) 
>
> Résultats attendus : À quoi cela devrait-il ressembler ? (e.g. : le texte dans l'entête doit être aligné avec les menus déroulants, etc.)


## Pull requests

Pour contribuer au code, commencez par forker notre repo sur Github. Vous devriez voir apparaître quelque chose du type : 

```
https://github.com/user/app
```

Ensuite, clonez votre fork :

```
git clone git@github.com:user/app && cd app/
```

Ajoutez le repo officiel comme repo distant pour suivre les modifications :

```
git remote add upstream git@github.com:DemocracyOS/app.git
```

Créez une nouvelle branche avec un nom pertinent pour les modifications que vous allez contribuer. Quelques exemples: 

```
git checkout -b feature/some-new-stuff
git checkout -b fix/some-bug
git checkout -b remove/some-file
```

Codez vos changements (vous n'avez pas besoin d'exécuter ceci) :

```
vim somefile.txt
git add somefile.txt
git commit -a -m"adding somefile.txt"
```

Quand vous pensez que votre code est prêt, préparez-vous à le pousser en récupérant d'abord les modifications du repo principal :

```
git pull --rebase upstream development
```
(Vous aurez peut-être besoin de résoudre des conflits à partir du rebasage à ce moment là)

Après, vous pouvez pousser vos modifications sur votre fork, en faisant :
```
git push origin feature/some-new-stuff
git push origin fix/some-bug
```

Finalement, allez à `https://github.com/DemocracyOS/app` dans le navigateur et soumettez un nouveau pull request. Normalement, Github reconnaît que vous avez des modifications en attente dans une nouvelle branche et vous suggérera de créer le pull request. 

Les principaux contributeurs vont revoir le code et peut-être demander d'y effectuer quelques modifications avant que votre code soit poussé dans le repo principal. Merci pour votre temps et vos efforts ! 

### Conseils généraux

* Nous marquons les issues où l'on sollicite l'aide de la communauté par [help wanted](https://github.com/DemocracyOS/app/labels/help%20wanted), essayez de privilégier celles-ci. 
* N'effectuez pas de pull request sans avoir exécuté l'application par vous-même. Cela signifie que vous devez au moins réaliser des [smoke test](http://en.wikipedia.org/wiki/Smoke_testing_(software)) sur ce que vous avez fait. Si vous pouvez effectuer quelques tests avec votre pull requests, c'est encore mieux.
* Essayez de ne pas polluer votre pull request avec des modifications non testées. Préférez des modifications simples et courtes. Des commits sans rapport nous empêcheront de fusionner. 
* Les pull requests doivent toujours être dans la branche de 'développement', jamais dans la branche 'master'. 
* Tous les pull requests devraient être conformes avec les [Styles de code](coding-styles-fr.md).