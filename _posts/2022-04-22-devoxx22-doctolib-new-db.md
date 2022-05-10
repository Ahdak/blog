---
layout: post
title: "Doctolib a besoin d'une base de données plus puissante. Ok, mais laquelle?"
date: 2022-04-24
excerpt: "DevoxxFR"
tags: Devoxx2022 BDD Doctolib
feature: /images/DevoxxFrance-2022.jpeg
comments: false
---

# Doctolib a besoin d'une base de données plus puissante. Ok, mais laquelle?

## Présentation

Depuis 8 ans, Doctolib propose des rendez-vous médicaux à plusieurs millions d'utilisateurs en utilisant une base de données unique PostgreSQL.

Nous ne sommes plus très loin aujourd'hui des limites physiques d'Aurora (PostgreSQL managé par AWS): nous opérons une des plus grosses bases de données transactionnelles d'Europe et pourtant nous prévoyons de grossir encore pour supporter notre croissance. Certes, il serait possible de tronçonner cette énorme base de données (voir d'en mettre certaines parties sur d'autres types de storage). Mais chez Doctolib, nous aimons bien l'approche simple d'avoir une seule base :)

Dans cette session, nous exposerons:

- Les limites actuelles, nos besoins immédiats et futurs
- Les critères d'évaluation que nous avons retenus? Scalabilité, compatibilité du code, coûts, hosting ...
- Les différentes approches technique de tests
- Quels sont les solutions que nous avons choisit de tester? Et de ne pas tester?
- Les résultats de l'évaluation de 3 solutions: Spanner, Yugabyte, Citus.
 
Plutôt que de rechercher la solution idéale, nous essayerons de mettre en évidence les compromis qui ont été choisis.

Talk de Bertrand Paquet et David Gageot.

## Notes

BDD 25 TO
Table avec 7x10^9 lignes


Hébergement des BDD Aurora. => Arrivé après 7 ans d’évolution
- 100% postgres
- Storage distribué
- Auto-scaling
- Le nombre de reader scale automatiquement en fonction du traffic

Le detail de replication est très bas entre les reader/writer

Il y a des mécanisme dans rails qui permettent de gérer si les appels se font dans le reader/writer

Test de load mensuel :
- test de montée en charge automatique avec le double du flux 
- Mesurer si l’appli peut tenir pour savoir s’il faut faire un upgrade / augmentation de la taille de BDD
- L’idée est de mesurer la distance du mur

Tuning point
- Le covid avec les centre de vaccination
- Pic de charge
- 1 seul writer n’est pas suffisant

Options :
- BDD qui peut faire x10
- Plan B : split de la BDD

Culture Doctolib : 
- 1 seul code base
- 1 seule BDD
- 1 seule CI


Active record peut donner des idées

Les BDD :
- Spanner
- Yugabyte
- Citus MX
- Vitess

Les outils de load test
- dedicated dataset (4 TO)
- Covid use cases

## Spanner 

Spanner : Stockage distribué (fonctionne uniquement sur GCP)

- Surcouche sur Postgres
- Les tables sont distribués
- Le sharing est automatique et transparent
- Rebalancing live
- Connexion distribuée

Cons :
- Limitation des join -> revoir le schema de donnée
- 1 seule clé de sharing
- Adapté pour les analytic mais non pas pour du micro transactionnel



## Yugabyte

- chaque table est shardée
- Un shard = 1 master + 2 follower
- Scaling infini
- Ajout de machine = relabalancing auto
- Yugabyte multi-clouer