---
layout: post
title: "DevoxxFR 2019 - Applications Spring Boot efficaces"
date: 2019-04-18
excerpt: "Applications Spring Boot efficaces"
tags: DevoxxFR conferences Java SpringBoot
feature: /images/2019-04-19-devoxx/2019-04-19-devoxx-intro-affiche.png
comments: false
---

Talk de __Stephan Nicoll__ @snicoll  et __Brian Clozel__ @bclozel de Pivotal.

# Abstract
Comment peut-on améliorer l'efficacité et la scalabilité d'une application web existante? On pourrait complètement la réécrire, avec programmation plus concurrente, fonctionnelle, ou réactive. Mais est-ce que ça vaut vraiment le coup, sans mesurer et savoir où concentrer nos efforts?
Dans cette présentation, Stéphane et Brian vont travailler sur une application Spring Boot MVC existante pour la rendre plus efficace. Ils vont remplacer RestTemplate par WebClient et utiliser des opérateurs Reactor pour améliorer la scalabilité, sans tomber dans les pièges de la programmation concurrente.
Ils vont utiliser des métriques fournies par Spring Boot, en ajouter des personnalisées, et garder un oeil sur les gains de capacité dans des dashboards.


# Notes

Source Code, slides and vidéo [here](https://github.com/snicoll-demos/initializr-stats).

Différence spring mvc & spring WebFlux


Dépendances webClient
- WebClient remplace RestTemplate
- Web client builder deja fournis

Devtools :
- outils a rajouter dans les dépendances
- Accéder les recharges
- Ne s’occupe pas des librairies

Les métric SB 2 :
- micrometer-registry
- On peut rajouter un nombre de metric par label
- On peut rajouter des tags transverses

Promotheux :
- appelle pour avoir le status
- On fixe l’interval des appels
- Récupère bq de métric

> On a des opérateurs de timeout pour aller sur un système de backup

Corriger le comportement de l’IDE
- Au moment de compilation,  on peut rajouter des meta datas

Live Flux
- flux de données
- In a un serveur
- Vue live
- Opérateur share entre tous les clients qui souhaitent être notifiés sur les datas
- Observabilité

<img src="{{ site.url }}/images/2019-04-19-devoxx/spb2.png">
