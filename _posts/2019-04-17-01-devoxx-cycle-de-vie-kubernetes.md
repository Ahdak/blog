---
layout: post
title: "DevoxxFR 2019 - Cycle de vie des application Kubernetes"
date: 2019-04-17
excerpt: "Cycle de vie des application Kubernetes"
tags: DevoxxFR university kubernetes
feature: /images/2019-04-19-devoxx/2019-04-19-devoxx-intro-affiche.png
comments: false
---

Talk de __Charles Sabourdin__ @kanedafromparis et __Jean-Christophe Sirot__ @jcsirot


# Abstract
Lors de cette présentation, nous allons dans un premier temps rappeler la spécificité de docker par rapport à une VM (PID, cgroups, etc) parler du système de layer et de la différence entre images et instances puis nous présenterons succinctement kubernetes.
Ensuite, nous présenterons un processus « standard » de propagation d’une version CI/CD (développement, préproduction, production) à travers les tags docker.
Enfin, nous parlerons des différents composants constituant une application docker (base-image, tooling, librairie, code).
Une fois cette introduction réalisée, nous parlerons du cycle de vie d’une application à travers ses phases de développement, BAU pour mettre en avant que les failles de sécurité en période de développement sont rapidement corrigées par de nouvelles releases, mais pas nécessairement en BAU où les releases sont plus rares. Nous parlerons des diverses solutions (jfrog Xray, clair, …) pour le suivie des automatique des CVE et l’automatisation des mises à jour. Enfin, nous ferons un bref retour d’expérience pour parler des difficultés rencontrées et des propositions d’organisation mises en oeuvre.

# Notes

## Docker
- Docker vient du kernel Lunix
- Namespaces : avoir plus d’image docker que de VM
  - Isolation logique
- Isolation : au niveau kernel et pas au niveau VM. On peut utiliser des dockers machines
  - Au niveau name space et au niveau réseau
- Layers :
  - __Dive__ permet d’inspecter les layer de l’image
- Mise en commun des Label des images docker
- Eviter le root dans les conteneurs
- Breadcrumb


## Kubernetes
- Outil d’orchestration des images docker
- Il a pour but de gérer les resources pour les mettre à disposition des images dockers
- On gère des typologies
- Auto scaling
- Plusieurs stratégies de Déploiement

## Master :
- garant de l’orchestration du cluster
- Machine physique
- Pod : Unité atomique d'exécution du code
- Kubelet : Element local qui va communiquer avec
- Replication Controller : il y a le bon nombre d’instance
- Service : Abstraction réseau de la communication

kubectl : commande

Afficher la liste des alias : `cat ./~`

Source : Bilgrin Ibryham

On a des operations comme :
__initContainers__ : lancer un conteneurs qui permet de faire 2/3 trucs avant le démarrage
Hooks

## Commands
- Command : `kubectl explain`
- On peut récupérer les détails du pod en format yaml : `kg xxxxx -o yaml`
- On peut avoir des volumes avec des contraintes de read/write pour un seul POD



## Workflow :
- developpement en local
- Dev on cluster
- IC on cluster
- QA on cluster
- Staging on cluster
- Production on cluster

## Plugin docker de Spotify
- construire une image docker depuis son maven
- En local ca marche bien


Docker multi-stage build :
- on fait l’installation dans l’image docker
- Un premier étage qui fait le maven install
- Un autre étage : qui crée un environnent d’exécution du jar
- On construit une image en copiant un jar déjà buildé

Le principe :
- 1 image de build
- 1 image d'exécution
> MEILLEURE OPTIMISATION DES LAYERS

On utilise un Makefile pour faire le build de toutes les images



## Package HELM :
- Décrire les dépendances de l’app dans des fichiers yaml
- Utilise systeme de regitre standard
- On peut créer des hooks
- Permet de gérer le cycle de vie de déploiement
- On peut déployer plusieurs release du meme package
- On peut faire un dry-run pour tester si ca fonctionne
- Faire attention au invente & au guillemets dans le fichier de confina de Helm
- Voir sur GitHub __(repo Helm charts)__

## Tools
- BuildKIT
- Tiller : Gestion de package

## DTR :
- Registry
- Permet la signature
- Admission controller

> La sécurité est un compromis. Il y aura toujours des failles de sécurités et on sera obligé de vivre avec.


## Pipelines
- CI : Code -> créer un binaire
- C Delivery : pousser automatique le code avec des workflow
- C Deployment : il faut de l’expérience / confiance en fonction de la séniorité

<img src="{{ site.url }}/images/2019-04-19-devoxx/cd.png">

## Question : Version tag / purpose tag ??
<img src="{{ site.url }}/images/2019-04-19-devoxx/tag-version.png">

## Application lifecycle

<img src="{{ site.url }}/images/2019-04-19-devoxx/app-life-cycle.png">

## Scan tools :
- Qualys
- Nexus
- Deptrack
- snyk

> Peuvent prévenir des failles de sécurités
> On peut harmoniser en créant une image base


## 1,2,3 Hosting :
- on utilise des docker file et des base images : On doit avoir une &quipe OPS qui gère les images & la sécurité
- Accepter la complexité & l’avancée
  - On peut aussi avoir un décalage entre les versions gérée par les devons et les versions dont les applications en ont besoins
- Gérer la signature et la sécurité des images fournies par les extérieur


## Pitfalls :
- Absence de product owner
- Se perdre dans l’autorisation et les outils
- Manque de communication
- Build to prod (no test)
- Bq de versions
- Manque de management / support / acceptance
