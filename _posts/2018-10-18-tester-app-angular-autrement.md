---
layout: post
title: "Tester les applications angular autrement"
date: 2018-10-18
excerpt: "12&13 Tester les applications angular autrement"
tags: Angular 12@13 Testing JEST
feature: /images/2018-10-18-tester-app-angular-autrement/2018-10-18-tester-app-angular-autrement-affiche.png
comments: false
---

Ce post est un résumé du talk de __Fayçal Tirich__

Les slides sont disponible [ici](https://github.com/Ahdak/tester-angular-autrement/blob/master/Slide-Tester-Angular-Autrement.pdf).
Les sources sont disponibles sur [Github](https://github.com/Ahdak/tester-angular-autrement).


# Généralités

## Avantages des Tests automatisés
* anticiper les bugs
* Assurer meilleure qualité

## Tests Smells :
* Tests lents
* Tests mal-isolés
* Tests obsolètes

Ainsi, les test deviennent très vite des inconvénients.


Tout le monde a le même problème !! Même chez Google …

>« Move Fast and don’t break things

L'objectif est d'essayer de trouver un équilibre entre le nombre de tests développés et le taux de couverture souhaité.

Pour cela, il faut
* Minimiser les tests end-to-end
* Max de TU

## Les types de tests :
* __Tests Unitaires__ : Détail de l’implémentation
* __Tests Intégration__ : Le composant est une boite noire et on teste l’API publique
* __Tests End 2 End__: Tests de chaîne

Il faut faire des TI pour vérifier que la chaine est OK.

## Stratégie des Tests
* Outside-In (adapté au front)
* Inside-out

## Angular

Un composant angular :
* sorte de classe
* Écouter des est navigateurs pour réagir à ces events

Angular fournit :
* un TestBed
* Mécanisme d’injection de dépendances (pour injecter les mock)


State Manager :
* L’application est considérée une machine à état
* Redux-Like
* Permet d’harmoniser

Le workflow :
* DOM <— Binding —> Composant UI <——- State
* Il faut dispatcher une action pour passer d’un état à un autre. L’action va lancer une mutation de l’état.
* Le composant reflète le stade sur le DOM

Pour tester :
* il faut rejouer le scénario
* init store avec initial status
* Dispatch action
* Vérifier que l’état est OK


> Il faut faire une bonne conception pour avoir des tests simples et maintenables.


# Outillages :

## karma-jasmine
* old framework
* Lourds
* Lancent un navigateurs pour faire les TU

## Jest :
* Simple à configurer & implémenter
* Pas besoin de local storage
* Pas besoin de lancer un navigateur pour tester
* Test runner
* Outil sur les statuts
* Lib mock / spy
* Possibilité de débug sur l’IDE
* Insère le « snapshot testing »
* Dossier __snapshot__ qui contient l’état du composant
* Lors des TU, Jest va comparer l’état généré par les TU et l’état dans __snapshot__
* Le dossier __snapshot__ est push avec les sources


## Angular console
* Permet de générer un projet angular
* Préconfigure les TU

## IDE :
* Web Storm

## Cypress
* Test E2E
* Il n’utilise pas scillenuim
* Bq plus rapide
* Permet de debug
* Insertion de scénario facile
* On peut faire des stubs : Dérouter les appels DAO pour charger des JSON
* Permet de créer des variables pour créer des scénarios avec différentes valeurs sans dupliquer le code.
