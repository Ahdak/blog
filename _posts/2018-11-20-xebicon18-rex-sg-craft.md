---
layout: post
title: "Xebicon'18 - REX Société Générale - The Devops Craftsmen"
date: 2018-11-20
excerpt: "REX Société Générale - The Devops Craftsmen "
tags: Xebicon meet-up cloud aws devops rex sg docker
feature: /images/2018-11-20-xebicon18-intro/2018-11-20-xebicon18-intro-affiche.jpg
comments: false
---

Talk de __Igor Lovich__, Tech lead of tech leads and stand-up comedian et
__Wilfried Villisek Faucounau__, Devops lead and bass player chez Société Générale


# Abstract
**Le contexte bancaire complexifie la marge de manœuvre pour mettre en place une démarche Devops : régulateurs, protection des données, ségrégation prod/dev, processus de validation sécurisé... Tout pourrait paraître contradictoire avec un concept de collaboration étroite entre les dev et les ops. Nous allons vous présenter comment nous sommes devenus Devops, avons rendu l’ownership aux développeurs sur le pipeline de livraison en production sans sacrifier les aspects de sécurité et par la même occasion, avons réduit les coûts de livraisons. Dans la deuxième partie de la conférence nous vous présenterons comment nous avons évité la catastrophe en mettant une bonne dose de Craftmanship dans le setup Devops.**

# Notes
Transformation DEVOPS de ITEC/FCC/DRA

## Monolith
* Lent à livrer
* Peu de jobs / peu de build
* TTM très bas

## Transformation micro-service
* Resilient
* Fast to build
* TTM meilleurs
* Complexité augmente
* Trop de Build (build après chaque commit)

## Contexte bancaire
* Sécurité
* Régulation
* Pas d’accès aux images dockers publiques
* Puppet (sans puppet forge)

## DEVOPS
* Ce n’est pas une équipe entre les DEV & OPS
* C’est un mindset entre DEV & OPS
* Améliorer le feedback : Monitoring & alerting
* Automatiser le build / déploiement
  * Bannir les taches manuelles
* You code it, you own it
  * La responsabilité est donnée à l’équipe

> Le mindset c’est le plus dur à change

## Organisation
* Feature Team (Support qui vient après 4 ans)
* Création de chapter DEV / OPS
  * 2 backlogs :
    * 1 backlog feature
    * 1 backlog transverse chapter
* Outils de communication (email à bannir / skype n’est pas super)
  * Utilisation de Symphony
* BBL

## Guidelines
* Pas de taches manuelles
* OPS in crafmanship way : fait du code / outils
* DEVS can do OPS task

## Les outils d’automatisation
* Code
  * Générateur de micro-service
    * URLS
    * Repos
    * Job jenkins
* Build
* Deploy
* Infrastructure
* Certificate par exemple : Check et régénère le certificat et le stocke dans Vault

## Convention
  * convention de dommage
  * Split of the code from configuration et deployment
  * Nommage des branches qui drive les build par env

## Build
* Librairies de Jenkins file
* Les scripts OPS sont testés
* Pipeline automatisé

## Large scale application
* 350 repositories
* Github crawler : permet de chercher dans tous les repos / branch
* CI-Droid : fait le replace & fait des pull request pour les modifications

## Dashboard
* Voir les diffs des versions
* Id du dernier commit git (SHA de git)

## Monitoring
* Index Elastic search
* Dashboard Kibana
* Création des alertes
* Management visuel

## Take away
* Application qui sont bien écrite
* Communauté (entre chapter / guildes)
* Chapter TEC
* Livraison de valeur de manière incrémentale
* Vision produit
* Il faut traiter l’outillage comme le code
