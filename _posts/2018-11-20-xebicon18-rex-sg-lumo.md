---
layout: post
title: "Xebicon'18 - REX Société Générale - L’expérience AWS avec Lumo"
date: 2018-11-20
excerpt: "REX Société Générale - L’expérience AWS avec Lumo"
tags: Xebicon meet-up cloud aws
feature: /images/2018-11-20-xebicon18-intro/2018-11-20-xebicon18-intro-affiche.jpg
comments: false
---

Talk de __Akram Blouza__, Cloud Builder chez WeScale @akram_wewe et __David Caramelo__, CTO chez Société Générale @david_caramelo

# Abstract
**Lors de cette session, vous verrez que le passage au cloud avec AWS permettra de répondre aux exigences les plus pointues en terme de qualité et de sécurité. Après avoir présenté brièvement le projet Lumo (plateforme d'épargne participative dédiée aux énergies renouvelables) et son architecture initiale, nous rentrerons dans le vif du sujet pour vous exposer l’architecture AWS cible, en abordant l’ensemble des solutions apportées sur les aspects sécurité, résilience et haute disponibilité. Enfin, vous aurez un aperçu sur la solution apportée pour la chaîne de déploiement de Lumo dans AWS.**

# Notes

Lumo (acheté par la SG en fin 2017)
* Start-up qui fait du crowdfunding sur les energies renouvelables
* Projets energies vertes
* 33 Millions KHW injecté

## Audit sécurité
* Sécurité
* Scalabilité
* Resilience
* Audibilité
* Traçabilité
* Historisation
* Simple à l’usage

## Avant le rachat, il faut se maj
* Firewall
* Datacenter
* Stratégie back-up clair
* Sécurité application
* Compagne Pen-Test

## Objectifs
* Sécurité : capter / isoler
* Autolsatisation : Moins d’intervention humaine
* Auto scalabilité + résilience
* Auditabilité


## Sécurité
* Séparer les comptes entre les différents env (SEC, DEV PRE PROD)
* Pas d’intéraction entre les comptes
* Roles des users :
  * Watcher (Read only),
  * tinker (role DEV - deployer l’app sur l’env DEV),
  * keepers (Donne des roles)
  * settlers (touche à l’infra AWS)
  * ROOT
* Traçage des roles (dans un Bucket S3 crypté)
* Isolation réseaux
* Para-feu applicatif (Open Web application Security) de type AWS WAF
* Security Group
  * Gestion du traffic entrant
  * Tout est logé dans cloud-watch

<img src="{{ site.url }}/images/2018-11-20-xebicon18-rex-sg-lumo/security-group.png">

* Https (SSL)
  * AWS certificat Manager
  * Mécanisme de validation / renouvellement de certificat

<img src="{{ site.url }}/images/2018-11-20-xebicon18-rex-sg-lumo/https.png">

* VPN
  * Outils interne de l’entreprise sont accessibles uniquement par VPN
* Cryptrage S3 Bukcet
  * AWS KMS
  * Chiffrement centralisé
  * Intégré service AWS

<img src="{{ site.url }}/images/2018-11-20-xebicon18-rex-sg-lumo/cryptage.png">

## Résilience
* Pas d’interruption
* Une instance par subnet
* Base de donnée répliquée

> La résilience est la capacité d'une architecture à continuer à offrir la même qualité de service même si certaines ressources deviennet inaccessibles.


## Scalabilité
* auto-auto-casing group (AWS)
* Load balancer ALB
* Capacité souhaitée
* Montée en charge
  * Période de grâce
* Cloud Front
  * CDN : réseau rapide de diffusion de contenu
  * Mettre en cache des données statiques
  * Acces rapides au data
  * 134 Points de présence dans le monde

> La scalabilité est la capacité d'une architecture à croître verticalement (sizing des machines) et horisontalement (ajoutant des machines).


## Automatisation
* Terraform (Hashicorp)
* Monter des infra rapidement
* Centralisation des tfstate
* Maximiser l’utilisation des modules

## Déploiement
* Gihub
* Jenkins
* On profite de AWS pour gérer  au niveau de auto scaling group
  * Prépare l’instance
  * Pull depuis GitHub
  * Configure, pull et déploie Lumo
  * Il faut avoir un health check

<img src="{{ site.url }}/images/2018-11-20-xebicon18-rex-sg-lumo/automatisation.png">

## Amélioration
* Tooling à rajouter
* Dokcerisation
* Archivage
* Exploitation / monitoring
* Automatiser le provisionnent automatique d’un nouvel environnement
* Multi-région

## Take away
* Design sécurité sur AWS
* A nous de trouver le model le plus secure
* Projet super
