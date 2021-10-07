---
layout: post
title: "Profiling et monitoring avec le JDK"
date: 2021-09-29
excerpt: "Devoxx 2021"
tags: Devoxx21 JDK JFR
feature: /images/2021-09-29-devoxx21.png
comments: false
---
# Profiling et Monitoring JDK

Talk de Jean-Michel Doudoux @jmdoudoux

## Présentation
Java Flight Recorder (JFR) est une fonctionnalité de la JVM qui après activation enregistre des événements émis par son activité mais aussi celle de l’application et même du système d’exploitation avec un très faible overhead. Il est possible d’émettre ses propres événements et même de consommer des événements grâce à une API de Java 14. Depuis Java 11, JFR est open source et il est donc utilisable maintenant en production sans licence commerciale. L’exploitation de ces événements avec l’outil JMC peut être particulièrement appréciable lors d’activités de profiling ou de monitoring. Cette session sera l’occasion de faire un tour d’horizon de ces fonctionnalités qui peuvent s’avérer utiles pour certaines problématiques.

## Notes

La JVM est l’élement le plus important de la plate-forme Java
- execute le Bytecode
- Ramasse miette
- JIT
- Gère les interactions avec le système

On a donc besoin d’outillage pour profiling et monitoring. On a souvent difficultés de reproduire les même problèmes d’env.

Ils existent des outils commerciaux qui sont couteux et qui génèrent un Over-head important.

Il existent les outils JDK
- [Arthas](https://arthas.aliyun.com/en-us/)
- JFR : JDK flight recorder
- JMC : JDK mission control

A partir de JDK14, on peut exploiter le streaming des events du JFR

### JFR
- Intégré dans une JVM Hotspot
- Collecte des événements (JVM, Os …)
- Faible overhead (Ce n’est pas l’enrichissement du byte code via un agent externe)
- A partir de Java 11, on peut l’utiliser gratuitement
- JFR produit des recordings
- On peut mettre plusieurs JFR sur la même JVM avec différentes métriques

Les types d’évènement :
- Instant Event (Eg démarrage d’un thread)
- Duration Event 
- Timed events

Requestable event
- JFR collecte les evt dans un burgers et les dump dans un fichier binaire
- Activable via l’option -X:StartFlightRecording pour lancer le JFR

Mode de fonctionnement
- Avec JCMD, on peut rajouter dynamiquement le JFR sans arrêter la JVM : `jcmd <pid> JFR.start`
- On peut aussi demander le dump
- Un fichier XML permet de configurer les events à récupérer
- 2 profiles standard :
  - default : suivi de prod (monitoring overhead < 1%)
  - Profile (Overhead < 3%) pour le profiling avec un nombre d’enregistrement / capture plus élevé
- Les fichiers binaires sont exploités grâce à  un plugin JMC
- La version 8.1 requiert java 11 
- Il faut avoir la compatibilité entre JMC et la JVM utilisé
- On peut utiliser un Java Flight recorder plugin qui propose des plugins (Base d’Eclipse RCP)

>En Java 17, il y a 250 events et on peut avoir la liste intégrale

Fournisseurs :
- AdaptOpenJDK
- Azul
- Oracle

A partir de java 11, on a l’outil JFR
- On a la possibilité de créer des événements à partir de java 9
- On peut arrêter /démarrer un enregistrements
- On passe toujours par un fichier

Java 14 : Streaming du JFR
- On peut récupérer les événement auquel on est abonné sans passer par un fichier
- 2 étape : S’abonner et enregistrer un Handler qui permet de le gérer

La VisualVM (version 2) Permet d’ouvrir des dump JFR

### Testing
- Junit5 : peut lever des évents JFR (plugin JFR maven)
- JfrUnit : Ecrire des tests avec des vérification JFR (Requiert Java 16)
- On peut faire des TNR sur les métriques

```java
// Todo code sample
```


### Conclusion
- Utiliser JFR & JMC
- Puissant pour faible du monitoring/profiling
- Gratuit depuis Jdk8u272
- API utilisable (depuis java 14)
- Ecosysteme qui se développe autour de la JFR