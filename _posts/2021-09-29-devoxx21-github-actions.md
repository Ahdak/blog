---
layout: post
title: "Automatisation 360° avec GitHub actions"
date: 2021-09-29
excerpt: "Devoxx 2021"
tags: Devoxx21 Git devops CI/CD
feature: /images/2021-09-29-devoxx21.png
comments: false
---

Talk de Alain Hélaïli, Solution engineer chez Github.

## Description

Les outils traditionnels de CI/CD peuvent effectuer une quantité incroyable de tâches, mais leur déclenchement ne s’effectue en général que à la suite d’un push de code. Ce type d’événement arrive relativement tard dans le cycle de vie d’une nouvelle fonctionnalité, ce qui signifie que tout le processus amont est fortement manuel. Avant de prendre en charge les use cases de CI/CD, GitHub Actions a été pensé pour cette automatisation de workflow. Je vous propose de découvrir GitHub Actions à travers l’implémentation de différents cas d’usage afin que vous puissiez repartir avec une bonne idée du fonctionnement de cette nouvelle solution, mais aussi plein d’idées pour organiser votre projet de manière plus efficace.

## Notes

Le talk est orienté démo.

Nuxt : générateur d’application VueJS

Commande Shell CLI : gh 

Les pipelines sont dans le fonder Workflow

### DependatBot

- regarde les dépendances
- Mode proactif : Vérifie s’il y a des nouvelles versions des packages utlisé
    - NPM
    - Pom
- Fait les PR pour faire les changes

Dans le fichier CI, on peut détecter les évènements et déclencher des traitements.

L’objectif est d’automatiser les actions de workflow

Le fichier `ci.yml` définit les steps de la CI -> Jenkinsfile like. See actions repo in Github/actions/checkout

On peut créer un code space directement depuis github pour ouvrir un VS code sur une VM distante sur Azure

On peut également utiuliser les `Action composite` : fragment de pipeline : See repo devoxx-2021-node


### Take away

Les github-actions peuvent replacer :

- Jenkins (avec les jenkinsfile, steps, déploiement ..)
- JIRA (gestion des issuers)
- Le déploiement / VM / cloud / Docker


