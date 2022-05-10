---
layout: post
title: "La fin des architectures en couches avec l’approche hexagonale"
date: 2022-04-22
excerpt: "DevoxxFR"
tags: Devoxx2022 Hexagonale
feature: /images/DevoxxFrance-2022.jpeg
comments: false
---

# La fin des architectures en couches avec l’approche hexagonale

## Présentation

Attention, cette conférence peut donner des envies de refactoring ! As-tu plein d’annotations sur tes modèles ? Connais-tu un peu MVC, et les suffixes classiques Controller, Service, Repository ? Clean code, les samples de code de Spring Boot et Stack Overflow sont à peu près tes seules références d’architecture ? Dans cette conférence, on parlera des limites de ces modèles, et des différentes contraintes que cela pose sur le code. Vous découvrirez les principes de l’architecture hexagonale et de son mindset. Vous repartirez avec des exemples concrets et des différents scopes dans lesquels vous pourrez l’appliquer efficacement.

Talk de Benjamin LEGROS.

## Notes


JSON View pour avoir des vues JSON sur les objets

Le fond du problème : Des contraintes sont dans les couches

>Règle : Je ne dois pas contraindre mon domaine avec des contraintes de couches

Ports & Adapters : l’ancien nom des architecture hexagonale

Le problem : faire fuite la logique métier dans la logique technique

Le domaine ne doit pas fuite dans la technique

Les ports : des interfaces
Adapters : implémentation & les mappers

Pour la persistance, on crée des objets qui portent le JPA
Décorréler le code métier du moyen de persistence

Domain Driven Design (DDD)

On créer une classe ArticleId pp long
Préférer UUID plutôt que Long

Service : sans état


### Réutilisation de module

- Application : implémentation des couches
- Client : resource
- Domain : 
  - Classe métier
  - Service associé

### Réutilisation des dépendances

- Application
- Domain : Peut contenir Spring context / Spring transaction / lombok
- Duo (mapper & dot)
- 1 couche par implementation
  - Messaging
  - Persistence Jpa

Problème de l’approche : code coverage x module

Adapters : contient les ArticleEntity

### Take away :
- bq d’objets  à durée de vie courte
- Bq de mappers
- Démultiplication des classes
- Overhead applicatifs


### Objets métier

- Lisibilité fonctionnelle
- Test en isolation technique
- Gestion de contrainte de couche
- Meilleure integration avec CQRS & DDD
- Flexibilité dans le cadre des micro service

### Pas pour vous :

- logique fonctionnelle peu complexe
- Si le modle métier est petit
- Mauvaise gestion des objets à durée de vie courte
- Scripting (batch / changeling)

### Pour vous :

- agrégation d’objets
- Bq de ports / service externes
- Event sourcing & CQRS

