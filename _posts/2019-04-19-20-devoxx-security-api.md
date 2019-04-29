---
layout: post
title: "DevoxxFR 2019 - Comment bien se Faire hacker comme il faut ?"
date: 2019-04-19
excerpt: "Comment bien se Faire hacker comme il faut ?"
tags: DevoxxFR meet-up conferences java security API
feature: /images/2019-04-19-devoxx/2019-04-19-devoxx-intro-affiche.png
comments: false
---

Talk de __Julien Topçu__, tech lead à la SG.

## Speaker
__Julien Topçu__ Senior Lead Developer à la Société Générale, je suis un fervent défenseur du Software Craftsmanship. J'évangélise activement autour de DDD/Hexagonal Architecture, l'XP et le Kanban #NoEstimates.
Membre de la fondation OWASP, je m'efforce de transmettre à la communauté une philosophie DevSecOps.

# Abstract
Et encore une fuite de numéros de cartes de crédit sur internet! https://www.infoq.com/news/2018/11/british-airways-data-breach
C'est révoltant n'est-ce pas ? Mais attends, qu'est-ce qu'on fait nous pour s'assurer que notre appli n'est pas une passoire?
Dans cette live-coding-hacking session, venez découvrir les erreurs les plus communes en sécurité, que la grande majorité d'entre nous font sans même le savoir!
Après cela, vous ne verrez plus votre application de la même manière...

# Notes

Repo GIT : https://gitlab.com/crafts-records/pangloss

__Fondation OWASP__ : bonne pratique de sécurité
SQL Injection  : Utiliser les paramètres query
HRE : Humain readable text

## Bcrypt :
- (spring security)
- Integrée dans spring security

## Appel des WS :
- faire attention au persona
- Définir les droits
==> Acces control Layer

> Il faut rajouter le userId dans les logs pour afficher les users qui a fait es actions

## XML external entity
- On peut l’utiliser pour afficher le contenu d’un fichier de config
==> Il faut désactiver les XML external entities
==> Utiliser Jackon2objectMapperBuilder (de spring)

## CSRF
==> Limiter les authorised servers

## CORS : Cross Origin Resource Sharing
==> Pas de pre-flight
==> Utiliser CSCRF-Tocken
==> Server side security

## XSS (x-side scripting)
==> On peut faire du safe HTML (Hibernate validator)

## Dependancy track
==> Outil pour vérifier les CVE
