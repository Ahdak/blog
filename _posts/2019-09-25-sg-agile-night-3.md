---
layout: post
title: "Agile Testing Night #3"
date: 2019-09-25
excerpt: "Société Générale"
tags: meet-up conferences Agile TU
feature: /images/2019-09-25-sg-agile-night-3/2019-09-25-sg-agile-night-3.png
comments: false
---

Comme tous les ans, se tiendra la soirée de l'__Agile Testing Night #3__ au siège de la Société Générale. Le fil rouge principal de cette soirée était le testing en mode Agile et plus particulièrement le TDD.

J'ai assisté au début à la keynote `TDD is dead, long live to TCR` de Xavier DETANT , CTO [Zenika](https://www.zenika.com), suivie d'une table ronde  `Le BDD remplacerait-il le TDD ?` animée par __Bruno BOUCARD__ & __Thomas PIERRAIN__, les cofondateurs de la société [42skillz](http://www.42skillz.com).

# Keynote : Le TDD est mort, longue vie au TCR - Xavier DETANT (Zenika)

Le TCR introduit un nouveau concept __Test && commit || Revert__, qui est un peu différent du TDD, et consiste au workflow suivant :
- Ecrire un TU rouge
- Ecrire le minimum du code pour le faire passer
- Si le test passe alors `commit`, sinon `revert`

Cette pratique risque de créer beaucoup de frustration pour les développeurs, vu qu'ils vont devoir supprimer le nouveau code suite à un TU rouge ! La pratique du TCR pousse à faire des changement tellement petit pour limiter les problèmes de merges ==> Workflow limbo

Pour les actions importantes / urgente; voir la matrice de Eisenhower

> I hated the idea, but, I have to try

Plus de détails sur :
- [blog de Zenika](https://blog.zenika.com/2018/12/03/tdd-est-mort-longue-vie-tcr/).
- [Article de Kent Beck](https://medium.com/@kentbeck_7670/test-commit-revert-870bbd756864)

# Eisenhower Matrix
The Eisenhower Matrix, also referred to as Urgent-Important Matrix, helps you decide on and prioritize tasks by urgency and importance, sorting out less urgent and important tasks which you should either delegate or not do at all. (See [Eisenhower Matrix](https://www.eisenhower.me/eisenhower-matrix/))

|               |     Urgent   |   Less Urgent |
| :-------------: |:------------- | :--------- |
| __Important__|__Do First__ : First focus on important tasks to be done the same day.|__Schedule__ : Important, but not-so-urgent stuff should be scheduled.|
| __Less Important__|__Delegate__ : What’s urgent, but less important, delegate to others.|__Don’t Do__ : What’s neither urgent nor important, don’t do at all.|

# Part 2 : Le. BDD remplacerait-il le TDD ? - Bruno BOUCARD / Thomas PIERRAIN @42skills

## BDD
- Découverte
- Example mapping
- Atelier 3 amigos
- Formulation
- Gherkin

L’intérêt du BDD est d’avoir des scénarios utiles exprimés avec les mots du métier.

L'automatisation de BDD est Couteuse. Parmi les outils utilisés :
- [Cucumber](https://cucumber.io)
- [SpecFlow](https://specflow.org)

Parmi les problématiques remontées : Lors de la partie [Gherkin](https://cucumber.io/docs/gherkin/reference/), on passe du temps a convertir le besoin du métier en ligne Gherkin, au lieu de se concentrer sur la compréhension / échange sur les besoins métiers. Ainsi, on passe plus de temps sur l’accessoire que sur l’essentiel.

Il est donc conseillé d'utiliser l'__Example Mapping__ parce que le format est plus simple et moins contraignant que Gherkin. L'exemple mapping consiste à 4 type de cartes :
- Carte Story
- Carte Critère d'acceptation (Acceptance criteria)
- Carte exemple
- carte Questions

Cette pratique permet des interactions plus efficaces, tout en restant bien focus sur la story.

Pour plus de détails, voir [cet article](https://cucumber.io/blog/example-mapping-introduction/).

### Event Storming
Atelier pour définir les éléments qui ont de la valeurs au métier.

### Cucumber
C'est un outil open source qui permet d'avoir une documentation vivante du code, à travers des tests des spécifications métier. Ainsi, le business peut suivre l'évolution de production du code.

### ATDD
On a également parlé du __ATDD__ pout __Acceptance Test Driven Development__. Le principe est d'écrire un test de critère d'acceptance qui est en échec, ensuite itérer plusieurs boucle de TDD pour le passer en vert.



### GAUGE
[Gauge](https://gauge.org/index.html) tests are in Markdown which makes writing and maintaining tests easier. Reuse specifications and robust refactoring to reduce duplication. Less code and readable specifications means less time spent on maintaining the test suite.


## Types de tests
On peut différence les écoles :
- Outside-In
- Inside-Out

### London school : Outside-in & Mock

### Outside-in TDD
Le système est considéré comme une boite noire. Le développeur écrit un test qui échoue sur une fonctionnalité (basée sur des critères d'acceptance), et ensuite écris du code pour faire passer le test en vert.

Voir cet [article](https://medium.com/@erik.sacre/clean-architecture-through-outside-in-tdd-64a31de17ccf) qui explique le principe.

### Mock
- Répond la question & peut être questionné si il a utilisé pour les tests
- Enregistrer ce qui se passe
- Vérifier si le mock a été modifié

### Stub
Programmé pour répondre a une question.

### Chicago School : Inside Out (Classical Approach)
Les problèmes qu’on pourrait avoir avec l’approche Inside out est le risque de se perdre dans le chemin, quand le système va grossir. L’avantage de l’outsider in est d’avoir un point de cadrage sur le résultat attendu.


# Conseils
- il faut toujours se poser la question suivante lors de l'écriture des tests ou du refactoring : __Est ce que j’ai utilisé le langage du métier dans mon code ?__ Ceci permet d'avoir un code qui répond au mieux aux exigences du métier, qui est plus maintenbale et compréhensible.
- Il faut tester les comportement, et non pas l’implémentation.

# Katas
- [Kata bank](https://github.com/sandromancuso/Bank-kata)
- [Kata Diamond](https://blog.crafting-labs.fr/2018/01/29/diamond-kata/) : Kata ou on revisite a chaque fois les règles métiers :
