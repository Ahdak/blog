---
layout: post
title: "DevoxxFR 2019 - Inside the JVM"
date: 2019-04-19
excerpt: "Inside the JVM"
tags: DevoxxFR meet-up conferences java JVM
feature: /images/2019-04-19-devoxx/2019-04-19-devoxx-intro-affiche.png
comments: false
---

Talk de __Sylvain Wallez__ @bluxte, Tech Lead Cloud, Elastic.


# Abstract
Suivez le lapin blanc ! Comment passe-t-on de votre code Java à l'assembleur qui le fait fonctionner ? Manipuler des concepts de haut niveau nous a fait oublier ce qui se passe "sous le capot", qui est pourtant une des clés pour écrire du code efficace.
A partir de quelques lignes de Java, nous partirons en exploration dans les différentes couches qui participent à l'exécution de votre code : compilateur, byte code, structure de la JVM OpenJDK, JIT, HotSpot, méthodes intrinsèques, assembleur généré...
Une présentation de vulgarisation accessible à tous, dont l'objectif est de susciter votre curiosité pour que vous aussi, vous partiez en exploration !

# Notes

Blog Client couch base : JVM profiling - Lessons from the trenches

Dans la méthode `Enulm.values()` —> on alloue bq de mémoire


On rajoute une première optima :
- Fast path of common values

Profiling mémoire :
- l’utilisation de la mémoire
- Mémory leak
- Pression sur le GC (objet avec durée de vie très faible)

Pour voir ce qui se passe sur la JVM :
- Profiler
- `jmap -histo $numero_process`


Pour voir les objet en vie `-histo:live` (perform GC first)

## Tools :
- Java Mission Control :
- Free a partir de JDK 11
- Java fight recorder :
- Agent de monitoring léger qui fait de l’échantillonnage
- Tracker l’origine des allocation mémoire
- Activité GC

## Optim 2 : Sortir la méthode `values()` dans une constante
- Pas de charge Mémoire
- Pas de charge GC

WeakReference dégagent quand on a des Full GC

Outil java -c —> affiche le byte code

On voit dans le byte code qu’on fait un clone au niveau de la méthode `values()` pour créer une copie défensive.

### Constant pool :
- c’est un graph
- Des objets qui ont un type
- Des chaines de caractère UTF 8

### Interprétation du langage
- `[` —> array
- `L<>` : class name
- `IJSBC` ==> types primitif
- `F,D` : Float Double
- `Z` -> boolean
- `v` -> void


### JMH
- outil de benchmark
- Projet openJDK


## Solution 3 : Prépare lookup array

### Dangers JMH :
- bien trouver le niveau d’optim
- Optimiser que le hot path
- Ca prend bq de temps

### Méthodes intrinsic
- macro C++ dans tous les sens
- Ne pas passer par le compilateur, mais utiliser directement les méthodes de la JVM
- Exemple :
- `String.indexOf()`
- Fichier `vmSymbol.hpp` : a lire pour comprendre les méthodes optimisés

## Conclusion
- Connaitre vos outils
- Être curieux
- Attention à la perte de temps
