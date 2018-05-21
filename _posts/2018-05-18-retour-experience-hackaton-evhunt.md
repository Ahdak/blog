---
layout: post
title: "Retour d'expérience sur le Hackaton Ev'hunt à la Société Générale"
date: 2018-05-18
excerpt: "Retour d'expérience"
tags: Watson IBM Hackaton Springboot JHipster Angular
feature: /images/2018-05-18-retour-experience-hackaton-evhunt/2018-05-18-retour-experience-hackaton-evhunt-affiche.jpg
comments: false
---

# Introduction
Je vous présente dans cet article mon retour d’expérience par rapport à mar participation au Hackaton Ev'hunt à la Société Générale les 16, 17 et 18 Mai 2018.

<img src="{{ site.url }}/images/2018-05-18-retour-experience-hackaton-evhunt/sg-logo.png">

L’objectif du Hackathon est de créer une plateforme pour faciliter le contact RH, Manager et Candidats.

> "La perfection est atteinte, non pas lorsqu’il n’y a plus rien à ajouter, mais, lorsqu’il n’y a plus rien à retirer". - Antoine de Saint-Exupéry.

J'ai fait partie d'une équipe de 4 développeurs JAVA : Rafik, Fabuela, Meng et moi même.

<img src="{{ site.url }}/images/2018-05-18-retour-experience-hackaton-evhunt/presentation.png">

# Notre solution

## Présentation de la solution
On a pensé une solution de bout-en-bout qui a pour objectif de :
* Uploader un CV en moins de 20 secondes
* Accélérer la prise de contact dans les forums
* Faciliter le contact entre RH / Manager / Candidat
* Faciliter le suivi des candidatures Afficher des statistiques

## Architecture
<img src="{{ site.url }}/images/2018-05-18-retour-experience-hackaton-evhunt/architecture.png">

# Techniquement

## Framework utilisés
* Développement Android pour le client mobile
* Springnoot pour les Web service
* Watson pour la partie IA
* Angular 4 pour le client Web (Géré par JHipster)
* ELK Stack pour le monitoring

Le développement **Android** et **ElsticSearch** a été assuré par Rafik. La partie Front en **Angular** a été entièrement codé par Meng et Fabuela. J'ai intervenu essentiellement sur la partie **Watson** et la partie **Web Service**.

# Watson
J’ai utilisé leses services Watson pour le parsing des Cvs :
* WKS pour l’entrainement du modèle (Machine learning)
* NLU pour le parsing des textes.

## Watson Knowledge Studio
Je suis parti d’un modèle assez simpliste des CVs. J’ai utilisé Watson Knowledge Service pour l’entraînement du modèle. Le modèle étant assez simple, j’ai utilisé que 12 exemples uniquement (faute de temps aussi).
<img src="{{ site.url }}/images/2018-05-18-retour-experience-hackaton-evhunt/wks.png">

## Natural Language Understanding
La majorité des champs ont été correctement détectés, ce qui était suffisant pour le Hackaton.
Les tests ont été effectués par [Watson Api Explorer](https://watson-api-explorer.ng.bluemix.net/apis/natural-language-understanding-v1#!/Analyze/analyzeGet)

## Contraintes du modèle :
* Le parsing du nom et prénom
* Pour les expériences professionnelles : Si des expériences sont présentés sur plusieurs lignes, alors, on ne peut pas entraîner le modèle pour les détecter.
On n’a pas utilisés les relations pour gagner du temps

# Les point forts de la solution
* Micro-services : Flexible, scalable, multi-plateforme
* Application Mobile pour fluidifier la prise de contact
* Gestion des profils et des droits améliorée par JHipster
* Plateforme de gestion de statistiques (Elastic Stack)
* Watson (ou autre IA) pour le parsing des CVs

# Les potentielles évolutions
* Gestion complète du workflow d’embauche par type de contrat
* Matching intelligent entre le CV et les offres existantes
* Chatbot pour la récupération des profils
* Connexion au calendrier Outlook

# Contraintes
* Le configuration de JHipster a été très couteuse en terme de temps.
* L’utilisation de plusieurs environnements techniques
* On était obligé d’être hors réseaux SG : Donc on n’a pas pu profité des toolkit SG (pour la partie Front) et de l'OCR déjà prêt pour parser les CV.
* On a pensé plusieurs use-case fonctionnels pour couvrir une solution de bout en bout

Pour la partie Watson :
* La préparation des données d’entrainement était couteuses
* La configuration des services


# Points à améliorer
* Préparation des données d’entrainement en amont
* Préparation du WKS en amont
* Configuration de JHispter
* Initier les repos Git

# Conclusion
Ce Hackaton était une expérience très intéressante sur le plan technique et humain.
