---
layout: post
title: "DevoxxFR 2019 - La boite à outils du chaos engineering"
date: 2019-04-17
excerpt: "La boite à outils du chaos engineering"
tags: DevoxxFR meet-up conferences chaos-engineering
feature: /images/2019-04-19-devoxx/2019-04-19-devoxx-intro-affiche.png
comments: false
---

Talk de __Sylvain Maucourt__ @sylv3k


# Abstract
Votre application est parfaite, codée au p'tit oignons... certain? Ajoutons donc du chaos et regardons ce qu'il en est. Via ToxiProxy et Chaos Monkey For Spring Boot, nous allons volontairement perturber une petite application et pourquoi pas l'améliorer !


# Notes

> Chaos Engineering : Créer des chaos pour rendre l’application plus résiliente

### Chaos Monkey for spring boot
- [Github](https://github.com/Netflix/chaosmonkey)
- Librairie java à ajouter dans maven
- Il faut activer le profil spring
  - Détecte les beans & active certains assaults
    - Latence
    - Remonter une exception volontairement
    - Killer l’application
  - Tout est configurable dans le fichier application.yaml

### Toxiproxy
- [Github](https://github.com/Shopify/toxiproxy)
- Proxy qui se met en périphérie du code
- Tester le code comme une boite noire
- Test de connexion BDD
- Ecrire des scénarios pour perturber le traffic
- On l’intègre avec des TU
- Il y a une partie serveur qu’il faut lancer

### Corrections
- Comportement anomal / exception
  - Utiliser Hystrix (circuit breaker)
    - Rajouter une dépendances
    - On rajoute les annotations sur les méthodes du controller
- Si de la latence est rajoutée :
  - Hystrix permet de se protéger

<img src="{{ site.url }}/images/2019-04-19-devoxx/chaos-tools.png">
