---
layout: post
title: "AWS vs GCP vs Azure"
date: 2021-09-29
excerpt: "Devoxx 2021"
tags: Devoxx21 Cloud AWS AZURE GCP
feature: /images/2021-09-29-devoxx21.png
comments: false
---

# AWS vs GCP vs Azure

Talk de Laurent Grangeau @Laurentgrangeau, Tony Jarriault @jarriaulttony et Olivier Dupré @olivierdupretec.

## Description
Tout le monde connaît ces 3 clouders publics majeurs. Mais... qu'ont-ils réellement en commun ? Quelles sont leurs différences profondes ? Le choix pour l'un des 3 est-il une question de coeur, de compétences disponibles ou capacités techniques ?
Faire le tour complet de chacune de ces plateformes prend déjà plus d'une journée. Alors faire le tour des 3 de manière exhaustive lors d'un talk est utopique. Nous irons donc droit au but et nous focaliserons sur les services majeurs, les plus utilisés et ceux pour lesquels la comparaison est la plus intéressante.

## Notes

Les Clouds représentent 61% des parts de marchés. L’idée du talk est donner un overview global des différence entre les clouds.

Il 4 types de service :
- IAAS : Couche infra sur laquelle on déploie
- CAAS : Gestions des applications en tant que micro-service conteneurs
- PAAS : Permet de gérer l’auto scaling 
- FAAS : Logique métier sans se soucier de l’infra pour exécuter le workload

### IAAS : 
Dans ce cas, il faut penser à 
- La localisation
- La réglementation
- Les types de calcul (data / Machine learning / compression / cryptage ...)
- Network :
  - AWS : VPC regional
  - GPC : VPC is global
  - AZURE : HUB & SPOKE
- Availability (SLA des VM)
  - GCP 99,99
  - AWS 99,5 => SLA sur les AZ uniquement au début, après ils ont rajouté des SLA sur les VMs
  - AZURE 99,5
  - Chaque loader met des loader dans la région. On trouve 3 AZ (data center). Si un data center (AZ), tombe, le traffic est transféré sur l’autre AZ

Si des VM tombent, le provider s’engage à faire les restant & le scaling. Point fort d’azure sur l’availability set (il faut le coder pour les autres).

#### SLO & SLA

Le cloud ne garantit par la perte du CA. Le client du cloud définit son RTO & RPO pour autoriser l’indispo / la perte des datas. Il faut trouver un équilibre entre la haute dispo et la tolérence aux pannes.

Auto shutdown
- AWS : instance scheduler
- AZURE : Build-in every VM
- GCP : instance scheduler on every VM

#### IAAC Azure (Infra as Code)

- Azure Resource Manager qui permet de décrire l’infra & la déployer
- Define blueprint
- [Azure bicep template](https://docs.microsoft.com/fr-fr/azure/azure-resource-manager/bicep/overview) : syntax particulière d’Azure ARM pour un code avec une meilleure syntaxe
- AWS : 
  - Cloudformation pour créer son cluster / Stack applicative
  - AWS Cloud development Kit qui permet du taper le code pour créer de l’infra

GCP : Cloud deployment manager (template YML) avec des stocks python

OpenShift : K8s encapsulé par redHat

L’IAAC génère un fichier json qui décrit toute l’infra. La taille du fichier peut aller jusqu’a 10K lignes de Json.

#### IAAC Terraform de Hashicorp

- Fonctionne avec tous les clouds providers
- 4 commandes : int, plan, apply, destroy
- 1 seul langage pour tous les providers (y compris on-premise)


#### Pulumi

- Voir [Pulumi](https://www.pulumi.com)
- Langage infra-as-code 
- Open surouce
- Permet de céder l’infra en Java / JS / go …
- Permet de piloter l’infra à partir du code
- Service EC2 : création de machine sur AWS

### IAAS in a nutshell 

- On ne fait pas la même chose on-premise & sur cloud
- Faire la micro-segmentation
- CI/CD pour créer les images avec le patch
- Lift&shift : a ne pas faire (prendre l’appli telle qu’elle est et la mettre sur le cloud)
- On ne peut pas comparer le legacy (acheté il y a plusieurs années) & le cloud
- Nouveaux outils & Nouveaux usages


### CAAS

#### AWS 

- Use ECS & Fargate (micro-VM)
- EKS (K8S) est récent et limité en terme de noeud
- Model (pay as you GO)

Comparatif :
- Azure : Active directory est le point fort (non présents chez AWS / GCP)
- GCP : 
  - Multi-pools (15K nodes) 
  - integration native sur les métrique K8S
  - CloudRun : a destination des développeurs (version Bêta) : Accès vers une API K8S & google gère
  - gitlab.com est bien branché avec le GCP

>GCP est la meilleure solution pour faire du K8S (Google est le créateur de K8S)

#### Service Mesh

> Micro service lead to Death Star Architecture

Comparatif
- GCP : Anthos service Mesh : Le leader 
- GCP : [CloudRun](https://cloud.google.com/run)
- Azure : Service Fabric Mesh

Les speakers on publié une Web-paper : All as Code

Le temps d’apprivionning & installation de la machine met bq de temps.

Pour le cas d’AWS, il y a cas qui existe qui est le Chaos engineering.check tool : [Spinnaker](https://spinnaker.io/docs/setup/install/providers/aws/)

### CAAS
- Grosse tendance pour les flounders
- Lead by K8S
- Il faut rajouter du tooling

### PAAS

- Elastic Beanstalk peut être utilisé pour le déploiement des applications sur AWS
- Le Clouders fait le travail des OPS sur le on-premise, et les OPS travaillent sur l’automatisation

Le métier de l’OPS est en train changer pout déléguer bq de choses aux clouders :
- Creer l’env 
- Démarrer la stack
- Meetre en place le VPC, Firewall …
- Pas de config à faire
- Load balancer en frontal
- Rajouter les variables d’env sans recompiler le code

Le PAAS permet :
- La configuration de l'env
- Deploiement Slot -> créer un nouvel env
- Mettre du monitoring, scalabilité manuelle ou auto

Sur AWS, l’application tourne sur le port 5000 (par défaut)
AZURE propose des serveurs WEB pour déployer sur cette chaine de serveur
Resource Group est propre qu’AZURE. Le plugin maven permet de faire le déploiement

#### Bleu-Green Deployment : 
Faire en sorte de l’url de la prod pointe vers un autre env (garder la prod & next-prod up en même temps) sans faire le déploiement automatiser

Quel est le niveau service que je vais prendre : 
- 1 seul déploiement
- Premium : 20 slots de déploiements de 20 versions de l’application

GCP
- AppEngine est en place depuis 2008
- Pas de Docker pour AppEngine
- Plugin maven qui va tout piloter / créer / déployer

#### PAAS in a nutshell
- Pas de gestion de réseau / infra -> c’est le CP qui s’occupe
- Ca coute plus cher, mais on réduit l’OPS capex
- On shift left sur l’infra


### Serverless

Nouveau Paradigme mis en avant par les Cloud Providers : Déployer du code application sans se soucier de l’infra
- facturé qu’a sa consommation
- Scaling automatique
- Scaling to 0
- Pas de management d’infra

Sur le marché :
- AWS Lambda
- Azure Cloud fonction
- GCP Cloud functions

Il y a des BDD serverless 
- BigQuery
- Azure SQL server Serverless
- AWS Arora Serverless

>Arora existe aussi en mode PAAS

GraphQL : 
- Released on 2015
- Avantage GreenIT
- Consomme que ce qu’on a besoin pour un use case donné
- Seul AWS propose ce type de service (amplify / appSync)

## Résumé
- Plus on monte dans les couches, 
  - plus les Cloud Providers s’occupent de l’infra
  - plus on est agile/rapide dans ce qu’on va mettre en place -> TTM bas
- Déploiement plusieurs fois par jour
- Quand on fait des cloud, on fait différemment sur le code / deploy on-premise
