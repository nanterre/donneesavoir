---
layout: post
title:  "Les notifications dans DemocracyOS"
date:   2016-09-22
categories: doc
tags: doc
---

Le module de notification a pour but d'enregistrer tous les événements de l'application qui peuvent concerner un utilisateur. Au niveau de la base de données, ces événements sont stockés dans la collection `notifications`. Sur l'interface, elles sont affichées dans la page `/notifications`, accessible à travers l'icône cloche près du badge utilisateur.

Jusqu'à maintenant, nous enregistrons trois types d'événement :
- Quand l'utilisateur répond au commentaire de quelqu'un d'autre
- Quand un utilisateur donne un vote positif au commentaire de quelqu'un d'autre
- Quand un utilisateur donne un vote négatif au commentaire de quelqu'un d'autre

# Le modèle
Dans `lib/models/notification.js` est déclaré le `NotificationSchema` qui définit le modèle Mongoose `Notification`. Il a les propriétés suivantes :
- `user`: `ObjectId` qui réfère à `User` dont la notification est ciblée. Dans le cas d'une réponse, c'est l'utilisateur qui a écrit le commentaire de réponse.
- `type`: `enum` qui représente de quel type de notification il s'agit.
- `comment`: `ObjectId` qui pointe vers un  `Comment`.
- `relatedUser`: `ObjectId` qui réfère à `User` qui a déclenché la notification. Dans le cas d'une réponse, c'est le répondant.
- `topic`: `ObjectId` qui réfère à un `Topic` relatif.

Il y a une méthode virtuelle, `url`, qui renvoie l'URL de l'objet cible (dans ce cas, le sujet qui détient le commentaire réponse/vote positif/vote négatif).

# Points finaux API

Les notifications sont créées et poussées dans la base de données via une API interne. Elles ne peuvent pas être créées via une API publique, mais elles peuvent être requêtées. L'API publique est située dans `lib/notification-api/index.js` et a un seul point final : `GET /api/notification/all`, 
qui délivre toutes les notification pour l'utilisateur enregistré (l'application interdit de récupérer les notifications d'un autre utilisateur).


# Pousser une nouvelle notification 

En interne, les notifications sont poussées de la collection `notifications` à travers le module `lib/notifications/index.js`, par appel de la fonction `send`. Cette fonction passe outre la fonction `NotifierClient#send()`, cela appelle en interne deux fonctions asynchrones : une pousse le document de notification et l'autre appelle `notifier` pour envoyer un mail de notification.

# Page de notification

La page des notifications est accessible en navigant vers `/notifications` ou en cliquant sur l'icône cloche près du badge de l'utilisateur. Le cycle de vie de la page est géré dans le module `lib/notifications-page`.

# À propos de `notifier`

Le module de notification ne remplace le module `notifier`. Ils travaillent ensemble et leurs responsabilités sont différentes : la responsabilité de `notifications` est de pousser un document de notification dans la collection `notifications` dans la base de données. La responsabilité de `notifier` est d'envoyer un mail utilisant le transport configuré.

# Roadmap

Nous allons changer le comportement de `notifier` en lui faisant questionner la collection `notification` afin de trouver les notifications non lues et construire et envoyer un mail de résumé notifiant en une seule fois les événements en attente de l'utilisateur. 

De même nous améliorons constamment le module `notifications` pour le rendre plus utile.