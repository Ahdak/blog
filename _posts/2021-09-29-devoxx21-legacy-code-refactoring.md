---
layout: post
title: "Live-Refactoring de Legacy Code avec la technique du Golden Master"
date: 2021-09-29
excerpt: "Devoxx 2021"
tags: Devoxx21 Refactoring LegacyCode
feature: /images/2021-09-29-devoxx21.png
comments: false
---
# Live-Refactoring de Legacy Code avec la technique du Golden Master

Talk de Philippe Bourgau @pbourgau @work_at_murex, coach en refactoring chez Murex

## Présentation
“Hier, j’ai continué mon refactoring. Je devrais avoir fini aujourd’hui.” Vous êtes vous déjà retrouvé à répéter cela pendant 15 daily meetings de suite?
Dans le quotidien des développeurs, refactorer le code legacy reste une des activités les plus compliquées et risquées. “N’y touchons pas !” est la recommandation la plus répandue. Malheureusement, nous passons au moins 80% du temps dans du code ‘Legacy’. Autant être prêt !
Venez découvrir, de manière très pratique, le golden Master et la méthode Mikado, 2 techniques de refactoring de code legacy, grâce à un live code kata. Vous comprendrez comment ces techniques:
	évitent l’effet tunnel
	découpent un gros refactoring en petites étapes déployables
	réduisent les risques
	contribuent à un rythme soutenable
	se complémentent pour un effet maximal
	peuvent être utilisées dans des contextes différents
Etes vous prêt à vous attaquer au Legacy ? Ça commence ici 😃

## Notes

Partie de jbrains/Trivia

On va créer des snapshot, et ensuite mettre en place la Golden Master strategy

Use librairie approvals => ouvre un diff

L’idée est de se base sur un out stream pour comparer les outputs via ce stream

Si ona des random, il faut utiliser des random déterministe

### Technique :
- any side effects
- On peut rajouter ses propres traces
- Attention au upgrade de snapshot
- Pas utilisable pour les tests de pipeline (a éviter dans la CI)
- On peut garder le code pour générer les snapshots

### Technique 2
- Mikado method : pour éviter les effets tunnels
- Needs : 
  - a goal
  - a non reg criteria

Mikado est une routine :
- essaie de faire le change & vérifie si on a des regressions
- Si la non reg passe -> commit
- Sinon, on rajoute des feuilles dans le graphe & on revert

>Pour la reflexion (graphe) See [coggle.it](https://coggle.it)

On reste focus sur l’objectif et on n’essaye pas de tout changer en meme temps.

### Avantages
- beaucoup moins risqué
- Arrêter le refactoring jusque’a demander la permission (si c’est petit, pas besoin de permission et se fait au fil de l’eau sans bloquer ..)
- Graph : communication tools (meme avec les non développeurs)
- Compilation issues : plantable graph
- Runtime issue : Dynamic graph

### Astuce
- loie de Linus -> Plusieurs yeux -> Pair coding / mob coding
- Use Mikado

Autres techniques à voir : Strongler / boble contexte / scaffolding DDD / test data builder

Pour la trace du système :
- il faut être opportuniste (les logs / les messages entre les service / les IO)
- Rajouter ses propres traces
- Use des toString des objets
















