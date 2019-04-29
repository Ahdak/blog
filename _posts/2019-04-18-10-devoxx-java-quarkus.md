---
layout: post
title: "DevoxxFR 2019 - De Java à un exécutif natif"
date: 2019-04-18
excerpt: "De Java à un exécutif natif"
tags: DevoxxFR conferences Java Quarkus
feature: /images/2019-04-19-devoxx/2019-04-19-devoxx-intro-affiche.png
comments: false
---

Talk de __Emanuel Bernard__ de Red Hat

# Abstract
Les microservices, la scalabilité instantanée et les plates-formes à haute densité comme Kubernetes nécessitent des applications à faible empreinte mémoire et démarrage rapide. Java n'était pas bien positionné car il favorise les temps de traitement aux dépens du CPU et de la RAM.
Plus maintenant.
Entre en scène Quarkus, une stack Java orientée microservices qui supporte vos composants favoris (Hibernate, Vert.x, Camel, RESTEasy ...) sur GraalVM et HotSpot avec une faible empreinte mémoire et un démarrage rapide. Tout ce qu'il faut pour tirer pleinement parti des containers.
La gestion de la donnée est souvent l'aspect le plus complexe : découvrons comment Quarkus gère la persistance avec Hibernate ORM. Venez explorer le live reload, notre vision de la persistance avec Hibernate Panache, l'environnement de test, la compilation native GraalVM et bien plus. Quarkus se vit plus qu'il ne se verbalise, attendez-vous à une démo détaillée.


# Notes

[Quarkus](https://quarkus.io):
- Révolution au niveau du java
- Supersonic subatomic java
- Stack to write java apps
- Focus sur la facilité de développement  (temps de retour entre dev & fix rapide)
- Simplification des cérémonies (les fichiers de config)
  - Centraliser sur le fichier application.properties
- Mode Live reload
  - IDE complice & Quarkus redéploie
- Accélérer le dev sur les cas commun (entités …)
- Génération des ligne de commande

Il y a un maven plugin qui permet de créer un exemple (projet vide)

On peut voir la liste de toutes les extension (dépendances) de Quarkus

## Packages
- Package java.ws.rs pour créer des WS
- PanacheEntity
  - Englobe aussi la partie DAO
  - Pas besoin d’entité manager
  - Toutes les méthodes DAO sont exposées dans l’entity
  - On peut fair du pain . Sorting requetage contextuel
  - Mode active record vs Repository
- Hibernate Validator
- Fichier impot.sql pour jouer des datas au démarrages de Hibernate
- Test running avec des annotations

<img src="{{ site.url }}/images/2019-04-19-devoxx/active-record.png">

## Découpage
- 1 monolith = 20 micro service = 200 fonctions
- L’idée est d’envoir des micro service indépendants du reste du monde

## Java
- lent a démarrer
- Consomme plus de RAM
- On charge bq de chose pour des petites applications


## Bénéfices
- compilation native permet de gagner en mémoire
- Temps de démarrage plus faible
- Réactivité : unifies réactif & impératifs
- Se base sur des frameworks déjà existants

## GraalVM
- __SubstrateVm__ : fait la compilation au moment du build
- Dead code elimination
- La compilation de la partie uniquement utilisée

<img src="{{ site.url }}/images/2019-04-19-devoxx/stack-java.png">

## Dark Side
- Pas de dynamic loading (pas de Class.loadForName)
- Il faut donner la liste des classes pour lesquels on va faire la reflexion
- Il faut lister les interfaces pour les Proxy dynamique
- Static initializers : inutilisé par defaut

## Comment ca marche ?
- on parse les fichiers de config
- Lire le classpath
- Scanner les classes (getters …) : récupérer les mata données
- Construire un méta model à utiliser au runtime
- Préparer les reflexion, proxies …
- Lancer les threads

> On fait tout ca en build et pas au démarrage
> Pas besoin de ces fichiers
> gain en espace Hotspot

## GraalVM needs
- Quarkus permet de récupérer la liste des classes d’hibernate ou on fait la reflexion
- Optimiser les dépendances

## Extensions Quarkus
<img src="{{ site.url }}/images/2019-04-19-devoxx/stack-quarkus.png">
