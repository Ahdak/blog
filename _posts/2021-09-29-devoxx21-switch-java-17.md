---
layout: post
title: "Les Méthodes Synthétiques Rêvent-elles à des Switch Expressions Électriques ?"
date: 2021-09-29
excerpt: "Devoxx 2021"
tags: Devoxx21 Java17 Switch JavaNews
feature: /images/2021-09-29-devoxx21.png
comments: false
---
# Les Méthodes Synthétiques Rêvent-elles à des Switch Expressions Électriques ?

Talk de José Paumard et Remi Forax

## Présentation
La version 17 de Java vient de sortir. C'est une version LTS, il se pourrait donc que les records deviennent votre outil de travail préféré avant longtemps. De nombreuses fonctionnalités ont été apportées depuis la version 11, LTS précédente, tant dans le langage que dans les API. Nous vous présenterons les éléments que vous utiliserez le plus dans vos applications : les nouvelles méthodes de l'API Collection et de l'API Stream, les Records, les Text Blocks et les Sealed Types ainsi que quelques mises en garde sur finalize() et les constructeurs des types enveloppe des types primitifs. Le pattern matching, développé dans le projet Amber a commencé à livrer ses premiers éléments : un nouveau switch et un nouvel instanceof construit sur le pattern de type. Nous en soulèverons le capot et examinerons le fonctionnement du pattern matching en détail. Au programme : du code, des patterns et des démos.

## Notes

[Dev.java](https://dev.java) contient de la documentation sur Java
[inside.java](https://inside.java) -> Podcast technique

Release Cadence : Il y a souhait (en cours de discussion) de créer des versions LTS tout les 2 ans

2/3 des applications en prod sont en Java 8

A partir de Java 17, Oracle JDK sera gratuit pendant 3 ans

### News entre Java 11 et Java 17

#### String :  

- String bloc : Le compilateur peut détecter les caractère d’indentation et les caractères d’affichage. L’alignement se fait grâce au bloc des 3 
- Next : interpolation dans la chaine de caractère

#### Arrays & List

- `Arrays.asList()` ne supporte pas le rajout et suppression d’élément. Par contre on peut modifier les éléments dans la liste `arraysAsList.replaceAll(String::toUpperCase)`
- `List.of` : On ne peut pas rajouter des éléments , et on ne peut pas modifier des éléments -> Pas le droit me tire les nuls dans `list.of`
- `List.copyOf` ne copie pas des listes avec des valeurs nulles. Si `CopyOf` voit que la liste est non modifiable, il ne faut pas la copie

#### Stream : 

- Size : use stream.count si la taille est de type long. Use method peek pour debug
- Le stream gere spliterator & gere des caratéristique 

> NB : Les méthodes intermédiaires dans les streams peuvent ne pas être toutes utilisées

Les effets de bord ne doivent pas être implémenter dans les stream

La méthode `toList` rajouté en Java 16
Optim : Pre-size la liste / retourne une liste non modifiable. Eg : To parse Integer : Use FlatMap as workaround

Methode : mapMulti : Biconsumer : 

### Records

- On peut déclarer des records à l’intérieur des méthodes
- final class
- Hérite de la classe Record 
- Num like
- Peut implémenter des interfaces
- Les composants du composants sont déclarés avec le record
- On ne peut pas rajouter des éléments d’instance
- On peut rajouter des champs / méthodes statiques
- Il faut absolument appeler le constructeur canonique => Bonne pratique
- Version raccourcis des classes pour récupérer la data
- On peut surcharger le constructeur canonique
- Il faut mettre la règle de validation dans le constructeur canonique
- On peut créer un constructeur compact
- La dé-sérialsiation dans les classes ne passe pas par les constructeurs, alors qu’elle le fait dans le record
- Excellent candidat pour les DTO
- On peut mettre des méthodes factory
- Le record peut donner un sens métier (au lieu de String)
- Il y a de l’over-head
- On ne peut pas changer l’accébilité du constructeur canonique
- Un record n’a pas d’encapsulation

> NB : soutv sur intellij permet d’afficher un tout de la dernière variable.

### Sealed types ?

- Limiter les héritages : Mot clé « sealed » : Lister les ensemble des sous-type direct
- On ne peut pas mock une interface sealted
- Pas de empty sealted
- Les sous types doivent être dans le même package / module
- Pas besoin de permit si c’est dans le même fichier java
- Pas de use dans les lambda
- Le sealed se propage dans le type. Il faut ajouter les mot clé non-sealed

### Pattern matching
- expression problem 
- Le switch sur le type + le permit sont exhaustifs (il ne faut pas mettre un default)
- Pouvoir spécifier des opérations sur des types déjà existants sans les maintenir
- Faciliter la maintenance
- Ecriture plus simple du pattern visitor (tue l’encapsulation)
- Problem lors du rajout du nouveau type
- Swith expression : pour returner la valeur de switch, on utilise yield
- Meme byte code générique

### Instanceof type pattern
- rajouter le nom de la variable (binding) 
- Attentions au generis
- On ne peut pas faire du switch sur les types primitifs

### Switch on type (preview)
- switch (instances like) See photo
- Equivalent a if-else avec des instanceOf
- Switch sur Null : case null / ou case Object (equivalent du dernier else dans une suite de if-else)
- Pattern guards