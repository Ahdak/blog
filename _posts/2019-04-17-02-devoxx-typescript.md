---
layout: post
title: "DevoxxFR 2019 - Typescript Js that scales"
date: 2019-04-17
excerpt: "Typescript Js that scales"
tags: DevoxxFR university typescript
feature: /images/2019-04-19-devoxx/2019-04-19-devoxx-intro-affiche.png
comments: false
---

Talk de __Thierry Chatel__ @thierryChatel de __Crealead__, Coopérative d’activité et d’emploi pour les indep.


# Abstract
TypeScript, est-ce une métamorphose du JavaScript, ou un nouveau langage qui ressemble davantage à Java ou C# ? Faut-il encore écrire du JavaScript de nos jours ? Mais alors dans quelle version : ECMAScript 2015, 2016, 2017 voire même 2018 ?
Cette université va vous présenter en détails TypeScript, et vous montrer comment il étend et remplace avantageusement JavaScript. On verra tout ce qu'il apporte en terme de syntaxe, le typage bien sûr et bien au-delà. On verra comment le code TypeScript est compilé et exécuté. On verra en quoi ce n'est plus du JavaScript, en quoi c'est encore du JavaScript, et comment le tout parvient miraculeusement à être un langage élégant et agréable à utiliser.
Une introduction détaillée pour découvrir et comprendre un langage aujourd'hui presque indispensable sur le web, mais aussi fort utile côté serveur.


# Notes

Type script :
- Fait par Microsoft
- La plus grosse partie c’est le tapage
- Il y a un TS playground sur le site de typescript


## TS :
- On peut faire des classes
- Structural typing
- On peut définir des propriétés
- Il existe des types Unions
- Le tapage n’est pas obligatoire, mais, il vaut mieux le rajouter pour avoir l’aide de l’IDE et éviter les problèmes.
- Outillage pour le debug
- Une classe peut implémenter une interface
- On peut rajouter des propriétés en mettant un get devant la méthode qui calcule un champs
- La mem chose pour un set
- Les get & set permettent de faire appel à des méthodes getters & setters ( pas comme en java)
- `private` / `protected` / `public`
- On peut avoir des propriétés `readonly`
- Des méthodes/ propriétés `static`
- Héritage entre classes
- On peut faire des classes abstraites
- On n’a pas besoin de dire qu’une classe implémente une interface. Il suffit que la classe remplie le contrat de l’interface`
- Une interface qui étend une autre interface
- On peut avoir des paramètres optionnel (en mettant la valeur ou ? Devant le nom)
- On peut faire de la déstructuration d’object
- Rest Operator : on étale des éléments (par exemple on étale u tableau dans un autre)
- `Async` / `Await` : La classe Promise
- Namespace (appelé anciennement module interne) :
  - Regrouper des choses qui se trouvent dans plusieurs fichiers
  - Il peut contenir des classes / interfaces …
- Un fichier est considéré comme un module
- On peut définir des alias de type ( type Name = String)
- On peut avoir des types paramétrés
- Les types de bases :
  - Primitif : `boolean, number, string, null, undefined, symbol`,
  - Object :  `Date, RegExp` …
  - `Void`
  - `never` : type pas possible
  - `Any` : n’importe quoi (tout peut fonctionner)
  - `Unknown` : On ne sais pas. Tout provoque des erreurs. Il faut faire des check de type (avec TypeOf avant d’utiliser les objets). Rien ne sera accepter avant de vérifier avant.
  - `this` : type polymorphisme qui représente l’objet
- Il ya des enumerations
- Intersection des types :
  - On peut définir des méthodes avec des arguments avec des objets qui est à la fois de deux types différent. Par exemple ‘let savePerson : (type1 & type2)’
    - On peut définir des intersection avec des énumérations (typage plus fort)
- Union type
  - Type 1 ou type 2 : Un des types de l’union
  - On peut mettre plusieurs types de retour possibles dans une méthodes
  - Dans un type union, on peut changer le type au cours de la méthode
- Literal type
  - `Const Bit = 0 | 1 ; /* peut prendre la valeur 0 ou 1 */`
  - `Const  n : 0 = 0 ; /* n a seulement la valeur 0`
- Type guards
  - On utilise `typeof` pour vérifier le type
  - On peut vérifier si une propriété est dans un objet `if (’name’ in Pet)`
  - Il peut aussi restreindre le type en fonction des valeurs des propriétés
  - On peut définir une function qui permet de gérer
  - Dans e type de retour on met `Pet is Fish`
- Le type `never` ne peut être accepté que si on détecte une boucle infinie ou un throw à la fin. Il ne faut pas que la méthode arrive à sa fin pour autoriser le type de retour never.
  - Il n’y a rien de type never sauf never
  - On peut l’utiliser dans ile cas de switch case sur les valeurs d’une enum pour forcer à gérer toutes les valeurs possibles d’enum
- Generics
  - `Identity<T> (param : T) : T`
  - Le generic peut faire un extends sur un autre type ’<T extends Pet>’
  - Operateur `Keyof` : C’est un type union sur des types littéraux.
  - `<K extends keyof T>` : K est un des types des propriétés de T
  - Sur les filters de type `is S`, il y a une gestions automatique des types. Si tous les items filtrés sont de type S, alors , le sous tableau est de type S,
- Typage extreme
  - On peut trouver le type d’une propreté `Person[‘age’]`
  - `Pick` : Prend certaines propriétés d’un objet
  - `Pluck` : Tableau avec les types des propriétés en question
- Types conditionnels
  - Selon certaines conditions, on peut avoir un type ou un autre
  - `T extends U ? X : Y`
  - `NonNullable<T> = T extends null | undefined ? Never : T ;`
  - Les types conditionnels sont distribués sur les types Unions

### Predefined
- `Extract` : Permet de chercher le type compatible avec un 2eme type
- `Exclude` : Enlève ce qui ne correspond pas
- `ReturnType<T>`
- `Parameters<T>` : Les types de paramètres des properties
- `InstanceType<T>` : Les types de l’instance de la classe
- `ConsctuctorParameters<T>`
- `Partial<T>` : Un nouveau objet avec des propriétés optionnelles (ayant tout en null ou undefined)
- `Required<T>` : Tout rendre obligatoire
- `ReadOnly<T>` : Tout est readonly (on ne peut pas les modifier)
- `Record<T>` :
- `pick<T>` : Dériver un autre type contenant juste une partie des propriétés

### Not predefined
- `Omit<T,K>` : exclut de T les type de K
- `Diff<T,U>` : Properties dans T et pas dans U
- `KeyNamesOfType<T,U>` : Les noms de propriétés de T qui sont de type U

### Mix-in
- L’idée est de rajouter des fonctionnalités en dérivant la classe parent
- On peut créer une classe dans les fonctions (sorte de classe anonyme)
- Decorateur (comme les annotations en Java)
- C’est une fonction qui renvoie une fonction
- On peut les mettre partout
- Il y a des fichiers de déclaration de type JS
- On peut installer des définition de types graces à OpenLayers
- Il y a des fichiers tex pour les fichiers react


### JS :
- Pas de déclaration / vérification de type
- Pas de classes
- Langage mono-thread
- Le `This` dans la fonction (au lieu des lambda) ne correspond pas à l’instance de la classe. Il faut passer pas des fat-arrow-function


> On rajoute private / public sur argument du constructeur pour déclarer des propriétés d’une classe



[Les Slides sont dispos !!](https://github.com/tchatel/conf-typescript)
