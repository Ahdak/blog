---
layout: post
title: "Intelligence Artificielle : Quelles réelles opportunités de transformation des métiers ? Illustration par le cas concret de la lutte contre la fraude au sein de la SG"
date: 2018-07-05
excerpt: "Résumé présentation - IA - Tech Week 2018"
tags: TechWeek2018 IA Data Meetup
feature: /images/2018-07-05-techweek-sg-2018-IA-Fraude/2018-07-05-techweek-sg-2018-IA-Fraude-affiche.jpg
comments: false
---


Cet article résume la présentation intitulée `Intelligence Artificielle : Quelles réelles opportunités de transformation des métiers ? Illustration par le cas concret de la lutte contre la fraude au sein de la SG` et présentée par 3 speakers de **Wavestone** : 2 Managers et 1 Data Scientist.

# Présentation Générale
Le Speaker a commencé par un état des lieux de l’utilisation de l’IA dans les projets IA des entreprises en France.

L’explosion des performances des machines (CPU, RAM …), le volume de données et les évolution des algorithmes sont des facteurs majeurs pour la mise en place des systèmes basées sur l’IA.

On constate qu’il y a une incompréhension de l’IA. En effet, la majorité des grandes entreprises mettent en place l’IA pour faire de l’économie, mais, ne s’attaque pas à une refonte générale de leur coeur de métier.

Il propose des transformation « AI driven » axées par
* IA
* Data
* Machine learning

Il a pris l’exemple de **Goldman Sachs** qui a mis l’IA dans son coeur de métier.

Grands domaines d’application :
* Relation & satisfaction client
* Performance client (cibler les offres)
* La productivité (transformation du process par l’IA pour limiter la partie humaine : coder les règles métiers et les faire dérouler par une machine)

# Devenir IA centric

## Les Grandes questions :
* Définir son niveau d’ambition (IA/DAta dans le plan stratégique)
* Définir une stratégie industrielle (bq d’acteur / fintech / GAFAMI) : Partenariat durable entre la Start-up et les grands groupes // Peur des GAFA (ils ont énormément d’avance) —> Il faut que les entreprises se développent en propre - mais incapacité à le mettre en oeuvre.
* Partenariat stratégique (Exemple Total-google)
* La responsabilité sociale (impact sur l’humain) - Comment l’IA sera un outil de transformation positive. EG : La poste : comment utiliser l’Ia pour valoriser la relation entre les postiers et les clients ?
  * travail humain first
  * Apporter plus de valeur pour faciliter le travail humain
  * Définir un model opératoire durable (Il faut s’organiser pour devenir un éditeur de logiciel)

## Transformation IA
* Data Strategy
* Smart Lab : pour animer les cas d’usage
* Data Management + compliance: Matrices la  data
* Savoir développer les algorithmes
* Data Sourcing
* Data @ scale

<img src="{{ site.url }}/images/2018-07-05-techweek-sg-2018-IA-Fraude/ai-transformation-lifecycle.png">


# Part 2 : GBIS - Fraude & data protection - Alexandre (Manager)

On se base sur les données des clients pour identifier les fraudes.

On est confronté à une problématique :
* Explosion du volume de données (exponentielle)
* Régulateurs de plus en plus exigeants + contrôles (RGPD par exemple)

Les équipes en charge de la surveillance ne grossissent pas aussi vite que les données
> IA est une voie intéressante


## Objectifs :
* Limiter les faux positifs (Limiter les pertes d’énergies)
* Permettre d’attraper les signaux faibles
* Augmenter la capacité de réponses + une meilleure qualité

## Use case 1 : Protection des données sensibles ( en partenariat avec une start-up)

L’objectif principal est de protéger les données sensibles dans la Banque privée GBIS. Pour cela, il a fallu localiser ces données dans les différents disques / shares...

L’identification des données consiste à mettre des tags sur des fichiers. On s’appuie sur l’IA :
* Pré-configurer le moteur
* Parcourir les fichiers
* Apprentissage à fur et à mesure : enrichissement de la base

L’idée est d’avoir un outil le moins contraignant et qui donne une vue précise et fine (sans impact user).

3 niveaux de confidentialité : _C1, C2, C3_

Les résultat étaient concluants. Une mise en prod est prévue asap


## Use case 2 : Data détection transfert (Partenariat avec une start-up)

L’objectif de ce projet est de maîtriser les IO du système d’infos, c’est donc la capacité à détecter les fuites de données

Les seuls systèmes exposées : mail - internet

Tout ce qui transite vers les site web est monitoré en mode basique : basé sur des seuils.
> Besoin de l’IA pour une meilleure identification.

250 Millions de lignes de log par jour : On n’a pas la capacité de les traiter avec les moyens classiques. L’IA par contre permet de
* Modéliser le traffic d’un user sur un site donné
* Etudier à long terme les données
* Corréler ces données sur 3/6 mois

La solution IA permet ainsi de constater plus facilement les anomalies avec plus de fiabilité et des critères de contrôles assez affinés.

L’efficacité du model actuel est moyenne : Encore beaucoup de faux positifs relatifs gros cookies, par exemple.

## Précision des systèmes IA
<img src="{{ site.url }}/images/2018-07-05-techweek-sg-2018-IA-Fraude/precision-recall-relevant-selected.jpg">

## Use case 3 : Login appropriateness control (DEV Interne)

L’objectif est de suivre des actions au système. En effet, l’usage des comptes est révélateur de certains comportement : Heure de connexion par exemple (trop tard le soir ou très tôt le matin).

Aujourd’hui, un contrôle des connexions au système pendant les congés est mis en place. Cette solution reste limitées en cas de télé-travail ou de décalage horaire, et fait remonter beaucoup de faux positifs. L’apport de l’IA dans ces cas est très bénéfique.

> L’IA est la solution face au nombre d’alertes des faux-positifs qui explosent.


# Part 3 :  (Nicolas - Data Scientist)

> Anomalies are patterns in data that do not conform to a well defined notion of normal behavior.
Varun Chandola

La détection d’anomalie consiste à détecter les patterns atypiques.

On se base sur :
* **Supervised learning** : apprendre à partir des comportement du passé pour prédire e futur maitrisé
* **Unsupervised learning** : gros volume de données - Détecter les comportement
* **Renforcement learning**
* **Online learning**

Le Unseupervided learning va donner les scores par rapport au data. L’humain va vérifier les notes.

## Approche Proof Of Concept
* Regarder les données / qualité pour voir si on peut répondre au besoins
* Regarder les historiques pour avoir une idée
* Faire l’audit des données

# Environnement Technique
* Spark
* Python
* Tensor Flow
* Code Open source adapté pour les besoin de la SG
* Réseau de neurone dans le réseau de la SG
