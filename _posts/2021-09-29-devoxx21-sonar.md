---
layout: post
title: "Sonar...Qube, histoire d'un petit analyseur de code qui devient grand"
date: 2021-09-29
excerpt: "Devoxx 2021"
tags: Devoxx21 Sonar CodeQuality
feature: /images/2021-09-29-devoxx21.png
comments: false
---
# Sonar...Qube, histoire d'un petit analyseur de code qui devient grand

Talk de Jean-Baptiste Lievremont et Christophe Zürn de SonarQube.

## Présentation
La plupart d'entre vous connaissent SonarQube, et certains l'appellent même encore Sonar. Le nom a changé mais pas que: de l'intégration de Checkstyle à l'implémentation de dataflow analysis, de la vérification de style à la détection de failles de sécurité, cette session retrace les changements profonds d’expérience utilisateur, de technologies et aussi d’organisation qui ont jalonné la vie du produit.

## Notes

Sonar devient SonarQube. Lors de la création de l’entreprise, c’était que du Java. Aujourd’hui, il y a plus que 20 langages de développement supportés par Sonar. Les équipes de développement de Sonar sont autonomes.

Clean as you code  : On se concentre sur le nouveau code => tout le code de l’appli s’améliore

Quality Gate : Barrière sur la qualité du code rajoutée (en fonction des nouvelles issues / coverage) ..

### Histoire 

- En 2013 : SonarQube 4.0
- En 2015, rajout du SonarLint pour les IDE
- En 2017 : sonar cloud
- En 2018 : SonarQube 8.0 -> Virage vers la sécurité
- Septembre 2021 : SonarQube 9.1 LTS (avec des images dockers validés)

### Les analyses

Analyse syntaxique :

- tokenisation (split  des caractère)
- arbre de syntaxe (Arbre binaire)
- Rajouter des infos sémantiques
- Check comment s’exécute le code : définir tous les chemins possible d’exécution
- Traint analysis => détection des vulnérabilités
- Rajouter des « secondaire location » pour la navigation dans le code

### Produits

- SonarCloud : Evolution de l’UX
- SonarLint : propose des fax pour les issues
- SonarQube Plugin : Afficher les issues du serveur en local

> Plus besoin d’utiliser PMD, check-style & findbugs

Sonar ne fait pas la SCA : check application composants (dépendances …). Sonar se base sur le code source. Techniquement, Sonar se base sur le compilateur ECG d’éclipse pour le parsing du code.

### Next Sonar versions : 
- Limiter les faux-positifs
- Check infra / IAAS
- Next analyseurs (Java 17+)

Concurrent : CAST(Audit One shot) / Checkmarx
