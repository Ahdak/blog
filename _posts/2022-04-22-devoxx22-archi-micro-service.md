---
layout: post
title: "Architecture microservices et cohérence des données : mais on fait comment pour de vrai ?"
date: 2022-04-24
excerpt: "DevoxxFR"
tags: Devoxx2022 Micro-service archi
feature: /images/DevoxxFrance-2022.jpeg
comments: false
---

# Architecture microservices et cohérence des données : mais on fait comment pour de vrai ?

## Présentation

Les architectures microservices ont le vent en poupe. Elles présentent de nombreux avantages pour mettre en place les bonnes pratiques DevOps et faire travailler en parallèle des équipes pluridisciplinaires autonomes. Evoluer du monolithe vers les microservices n’est pas un long fleuve tranquille… Les challenges ne manquent pas. L’un d’entre eux, et non des moindres, est la cohérence des données. Un des principes de base est que chaque service possède sa propre base de données. Quand une transaction métier invoque plusieurs services, on ne peut donc plus compter sur les bonnes vieilles transactions ACID des serveurs SQL. Quant aux transactions distribuées XA/2PC, oubliez les, elles sont jetées au pilori des mauvaises pratiques depuis bien longtemps ! Une fois ce constat établi, on fait quoi concrètement ? Comment peut-on répondre à cette problématique ? Dans cette présentation, nous verrons comment procéder avec deux approches : les SAGA et les LRA (Long Running Action). Un exemple concret basé sur MicroProfile et le framework Eeventuate Tram vous permettra de les appréhender et de choisir la bonne option pour votre projet Microservices

Talk de Jean Francois JAMES @jefrajames

## Notes

Challenges :
- identifier et borner les services
- Garantir la cohérence de données (entre plissures base de données métier)

Transaction ACID locale

<img src="{{ site.url }}/images/2022-04-22-devoxx22/micro-service-transction-messaging.png">

L’isolation max est serializable, mais, on est souvent sur un compromis

Transaction distribuée : 
- on fait appel à un transaction manager
- Non supporté par du NOSQL / Kafka

LRA/SAGA :
Rajouter une couche de coordination LRA/SAGA avec un ACID locale

4 solution :
- Microprofile
- Eventuate
- AxonIQ
- SEATA

Micro profile LRA
Eventaute SAGA

Plateforme eventaute :CQRS / Event sourcing / JPA …

Jager UI : pour voir ce qui se passe entre les servies

On doit implémenter une méthode compensante pour faire l’annulation/rollback de la transaction

Il y a des petits pièges :
- Pas de delais sur les appels des méthodes complete
- Pour la méthode compensate, on peut avoir des soucis avec le système distribué

Demo LRA 

<img src="{{ site.url }}/images/2022-04-22-devoxx22/micro-service-LRA.png">

Entente demo (mode asynchrone)

<img src="{{ site.url }}/images/2022-04-22-devoxx22/micro-service-LRA-Eventuate.png">

Techno dbsum ? Pour tail log

Zookeeper : permet e gérer la redondance des CDC

Outil Kowl ??

Nariana est le seul orchestrateur


Voir le code des démos

<img src="{{ site.url }}/images/2022-04-22-devoxx22/micro-service-resource.jpg">

