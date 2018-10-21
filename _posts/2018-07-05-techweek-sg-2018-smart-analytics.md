---
layout: post
title: "Smart Analytics"
date: 2018-07-05
excerpt: "Guillaume Rouduillere (ITEC/MKT/ARC)"
tags: TechWeek2018 IA Data Meetup
feature: /images/2018-07-05-techweek-sg-2018-smart-analytics/2018-07-05-techweek-sg-2018-smart-analytics-affiche.jpg
comments: false
---

Cet article résume la présentation de **Guillaume Rouduillere (ITEC/MKT/ARC)** par rapport à Smart Analytics.

L’objectif est d’améliorer la façon dont OPER monitors les process POST-TRADE. En effet,
OPER intervient dans le Booking + Reglement + réconciliation (cash break) + gestion client + gestion collat  dans POST-TRADE process.

# Problèmes
* pas de définition claire de ce qu’on veut faire  / du process (début, fin)
* pas les mêmes acteurs / produits
* divergence dans les outils pour produire des dashboards pourtant le meme process
* TTM très mauvais
* Temps perdu au détriment de la gestion des données
* Pas de capacité de Steering

# Alignement des équipes
* point de contact unique pour gérer les KPI
* Créer un dictionnaire commun
  * On explicite le prosess
  * On explicite les critère de monitoring —> difficile à aligner les concepts
* DATALAKE :
  * Single Warehouse pour le storage
  * Data processing capabilities
* Smart analytics :
  * 1 seule appli pour industrialiser les dashboard
  * data analytics

> Le coût de Production des dashboard : 50 FTE / an (Périmètre OPER pour MARK)

# Pourquoi améliorer ?
* proposer des choses pour améliorer les process : Ne pas faire le monitoring juste pour le monitoring
* Réduire les couts (Optimisation des process plan)
* Satisfaction du client  :TTM, qualité des transactions
* Business intelligence : Identifié les topologies de client (client toxiques : bq de post trade pour peu de valeur), développer des offres clients, ..


# Approche

## STEP 1
* **Business Object**: Quel objet métier sur le quel le process s’applique —> Mettre d’accord tous les stakeholder. Eg : Les volumes des trades : qu’est ce qu’on comptabilise
Comptabiliser les Action Event
* **Workflow** : Définir le cycle de vie d’un objet métier
* **Actors** : Les gens / process automatisé qui vont faire avance l’objet métier dans son workflow. Idélament, tout est automatisé.
* **SLA** : Service : Contrat qui maintient une garantie de service. EG : Il faut que le trade soit inséré dans le booking le jour ou il est négocié avec le client.

## STEP 2 : Définir ce qu’on veut mesurer
* **Efficacité** : Mesure si mes roches sont fait comme attendu par le client
* **Efficience** : rapport Effort / resource

## Indicateurs
* **Volumes** : nb items métier
* **Qualité** : statuts des process (cas de reword)
* **Automatisation** : Nb item sans intervention manuelle
* **Timeliness** : Nb d’item traité pendant une durée

## STEP 3 : Build DATAMART
**DataMart** : vue orientée métier

# Architecture
* **NORMALIZATION JOB** : Un job de normalisation : data lake distributed jobs allowing flexible and scalable normalisation of Raw data (HIVE, SPARK, OOZIE)
* **SMA WAREHOUSE** : contient touts les données des Process fonctionnels qq le process
* **SMA DATAMART  (OLAP CUBE)** : cuble contient dimensions et mesure

<img src="{{ site.url }}/images/2018-07-05-techweek-sg-2018-smart-analytics/archi.png">



# Visualisation des données
* Les users ont besoin de consommer un dashboard fini
* Application « Tableau » / SG Dashboard
* Les users experts permettent de comprendre et analyser les données et proposer des pistes
* Les data scientist ont accès au données au SMA Wharehouse (sans agrégation)

<img src="{{ site.url }}/images/2018-07-05-techweek-sg-2018-smart-analytics/data-visu.png">


> Les Process Owner définissent les process / value chain.

> Problème de gouvernance lors de MEP / alignement et convergence

> STP : Straight to Processing : indicateur pour voir s’il y a des interventions manuelles.


# Usages
* **OPER** : Performance par chaine de valeur / comment l’améliorer ?
* **OPER TEAM MANAER** : Performance de l’équipe
* **CLIC CRM** : Quelle l’efficience pp clients ? reste des SLA ?
* **OPER TOP MNAGEMENT** : Efficience relative
* **OPER FTO** : Performance des fonctions

# Bénéfices tangibles (quick-win)
* Décommisionner 40 old reports
* Feed back user : gain de temps lors des analyses
* Limiter l’use des macros / croisement des données entre les applications
* 1,5 Million € budget CTB + 40 FTE
* Gain en terme du sourcing (500K / an)

> DATAMART calculé à J+1

# Evolutions futures
* SMA WAHREHOUSE + DEV => PROCESS timeline application : Voir le workflow du trade post application. Solution technique : Indexer les données dans Datawhrehouse
* Accompagner les clients pour leurs montée en compétence sur Tableau
* Hackathon en octobre pour les users de Smart Analytics pour créer des nouveaux use-case
