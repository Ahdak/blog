---
layout: post
title: "Agile Testing Night 2 - SG"
date: 2018-07-05
excerpt: "Mutation Testing à la Agile Testing Night à la Société Générale"
tags: Mutation Testing Agile TDD SG AgileTestingNight
feature: /images/2018-09-25-agile-testing-night-sg-mutation-testing/2018-09-25-agile-testing-night-sg-mutation-testing-affiche.jpg
comments: false
---

La conférence a été présentée par __Benjamin HUGOT__ @BenjaminHugot et __Nicolas CHAZEAUX__.

# Constat

## Qualité des TU
On constate que la qualité des tests est impactée par la connaissance de l'application. En effet, notre feedback fait qu'on peut passer à côté de plusieurs tests.

## Code Coverage ?
Meme si la couverture est à 100%, on risque d’avoir des bugs / effets de bord :
* Ce n’est pas le meilleur critère pour savoir si la qualité
* Il permet juste de voir les points qui ne sont pas lancés

Comment quantifier la qualité des tests ?  ==> Mutation Testing

# Mutation Testing

Il ya un framework de mutation testing qui va effectuer des mutations sémantiques.

## Un mutant
* changer une constante, une variable …
* Supprimer / Changer une ligne de code
* Changer les comparateur > <= …

> Objectif : Il faut que les mutants soient en false. Si un mutant reste, c’est que le code n’est pas bien testé.


## Outils :
* PiTest pour faire les Mutations Tests
* mutt en Python
* StryKer en JS

In existe un plugin intellij.

## Résulats
* Le rapport du test est en Html.
* On peut spécifier les méthodes testées, les mutations … Tout est configurable
* Le rapport de coverage peut être lancé par CLI

> Impact : Amélioration de la qualité de livraison

L’objectif c’est de rendre la suite de teste assez robuste.

## Points négatifs
* La couverture 100% est très difficile, et n’est pas atteignable.
* Analyser les résultats d’analyse est compliqué
* Prend bq de temps —> Privilégier le build de nuit

## Utiles pour
* Application testée : il faut une couverture de test assez importante (+ 75%)
* Equipe avec Test first

# Passons au code

## Step 0

### classe Fonctionnelle

```java
    public class Calcul {

      protected int sommeClassique(int a, int b) {
          return  a+b ;
      }

      protected int sommePondere(int a, int b) {
          if (a < 10) {
              return a+b ;
          } else {
              return a + 2*b ;
          }
      }
    }
```

### Classe de Test

```java
    public class CalculShouldTest {


        private Calcul calcul;

        @Before
        public void setUp() {
            calcul = new Calcul();
        }

        @Test
        public void get_10_when_sum_classique_3_and_10() {
            assertEquals(10,calcul.sommeClassique(3,7));
        }

        @Test
        public void get_20_when_sum_pondere_12_and_4() {
            assertEquals(20,calcul.sommePondere(12,4));
        }

        @Test
        public void get_9_when_sum_pondere_5_and_4() {
            assertEquals(9,calcul.sommePondere(5,4));
        }
    }
```

### Mutation Testing report
run command ```mvn clean install -DwithHistory org.pitest:pitest-maven:mutationCoverage```

Vérifier les dépendences :
```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.pitest</groupId>
                <artifactId>pitest-maven</artifactId>
                <version>1.4.2</version>
                <configuration>
                    <targetClasses>
                        <param>net.dammak.mt*</param>
                    </targetClasses>
                    <targetTests>
                        <param>net.dammak.mt*</param>
                    </targetTests>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

Le rapport généré est le suivant :
<img src="{{ site.url }}/images/2018-09-25-agile-testing-night-sg-mutation-testing/step0-overview.png">
<img src="{{ site.url }}/images/2018-09-25-agile-testing-night-sg-mutation-testing/step0-mutant.png">


On constate ainsi qu'il y un cas qui n'a pas été testé.


## Step 1
On rajoute un TU qui vérifie le changement de la valeur limite __10__ de la focntion "sommePondere":
```java
    @Test
    public void get_18_when_sum_pondere_10_and_4() {
        assertEquals(18,calcul.sommePondere(10,4));
    }
```

C'esgt ainsi que le rapport passe au vert :
<img src="{{ site.url }}/images/2018-09-25-agile-testing-night-sg-mutation-testing/step1-overview.png">
<img src="{{ site.url }}/images/2018-09-25-agile-testing-night-sg-mutation-testing/step1-mutant.png">


Le code est disponible sur mon [Github](https://github.com/Ahdak/mutation-testing).
