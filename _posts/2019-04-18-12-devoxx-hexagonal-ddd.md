---
layout: post
title: "DevoxxFR 2019 - Hexagonal @ scale"
date: 2019-04-18
excerpt: "Hexagonal @ scale"
tags: DevoxxFR conferences DDD Hexagonal
feature: /images/2019-04-19-devoxx/2019-04-19-devoxx-intro-affiche.png
comments: false
---

Talk de __Cyrille Martraire__ @cyriux, CTO d'Arolla.


# Abstract
Les Microservices ont absolument besoin de DDD. La notion de Bounded Contexts, un ingrédient-clé de DDD, est l'outil de choix pour définir des contours de services qui ne vont pas mener au désastre au runtime et ou au moment de déployer. Ce talk vous donnera une meilleure compréhension de ce concept et de son intérêt dans le contexte d'une architecture de plus en plus distribuée. Surtout, vous découvrirez comment vraiment découper votre monolithe avec de nombreux conseils. Nous évoquerons aussi quand appliquer, ou non, le style Hexagonal, parce qu'il ne faut pas abuser des bonnes choses. Et tout cela avec du fun et quelques surprises !


# Notes

Random boundaries ==> micro services
- Probleme de dépendances
- Probleme de down / up
- Bq de meeting ==> Probleme de définition de bouderies


> Micro services ends DDD
> Community Paris Software crafters

### Comment définir les frontières  ?
- Split by layers
  - Facile
  - Mauvaise idée
- Split par technology
  - Facile
  - Peu marcher
- Domaine entity
  - C’est le drame …

> La réponse c’est domain Driven Design ==> La solution depuis 2011 qui ne vieillit pas

## DDD
- il faut couper par zone fonctionnelle homogène
- Ce n’est pas facile
- Des contours qui on t un sens métier
- Il faut apprendre le métier
- Bounded Context
- Un package qui contient tout ce qui partie d’un domaine métier

### Comment on sait si les bouderies sont bonnes ?
- on ne peut pas avant de tester
- Voir l’historique des commits
- On introduit des sous domaines pour limiter les if …
- Le langage est une source d’information

> Duplication = couplage : Le Dont Repeat Yourself ne marche pas à tous les couts
Si on centralise, on maximise le couplage


### Ou placer le curseur
- Dry + coupling || Isloation + Redundancy
  - Si on est sur un meme module maven
  - Si on est à grande échelle


Coupling ==> perte de cohésion

Chercher le contexte métier :
- le clustering des mots suggère du découpage
- Un bon domaine finit généralement en « tion » ou « ing »

> On découpe le modèle métiers en patates, sous patate … chaque patate représente un bounded context

### Event storming
 - Zone de pression technique qui conditionne le domaine
- On peut avoir différent type de base par module

> Pour se rassurer : on compare avec qq chose qu’on connait bien

Si on a des projets qui ne sont pas rattachés aux meme management, alors, il ne faut pas les mutualistes

Attendre jusqu’jusqu'à ce que les module soient un peu plus stable

Dans chaque, contour, on peut faire des trucs différents

## Archi hexagonal
<img src="{{ site.url }}/images/2019-04-19-devoxx/archi-ddd.png">


> Dana brendemeyer : Global architecture

### Hexagonal @ scale
- Intérieur : domain
- Extérieur : audience

On peut penser comme un SAAS dans l’entretprise.
<img src="{{ site.url }}/images/2019-04-19-devoxx/dep-ddd.png">
