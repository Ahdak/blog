---
layout: post
title: "DevoxxFR 2019 - Comment déployer un produit sur un cloud sécurisé ?"
date: 2019-04-19
excerpt: "Comment déployer un produit sur un cloud sécurisé ?"
tags: DevoxxFR meet-up conferences cloud security java
feature: /images/2019-04-19-devoxx/2019-04-19-devoxx-intro-affiche.png
comments: false
---

Talk de __Nicolas Cothereau__ et __Guillaume Attal__ de Thales Factory.



# Abstract
Quand on parle de Thales, on pense surveillance maritime, automatisation de lignes de métro, satellites …, mais rarement agilité ou DevOps. Pionnier de l'innovation technologique dans de nombreux domaines, Thales a créé la Digital Factory afin d'accélérer la transformation digitale du groupe et tester de nouvelles méthodes de travail. Nous y réalisons ces mêmes projets industriels et explorons de nouveaux marchés (gestion de flottes de drone, smart cities, …), dans une logique d’équipes auto-organisées et autonomes, responsables de l’ensemble du Delivery. Au travers de l’exemple d’un de nos projets, nous allons partager avec vous comment nous travaillons au sein de Thales Digital Factory : • déployer de façon continue des apps sur un cloud public qui répondent aux besoins de véritables users tout en respectant les contraintes de sécurité d’un grand groupe industriel, • mettre en place des pratiques de dev de nos différentes squads afin que performance et qualité soient au rendez-vous en production


# Notes

La sécurisation des applications est un enjeux.

Organisé par 15 équipes autonomes :
	-	PO
	-	DEV
	-	UI / UX
	-	Devons
Sécurité partner ==> rattaché à la sécurité

Pourquoi cloud public  ?
	-	Simplicité / rapidité pour créer des resources qui correspondent aux besoin (OPS)
	-	Passage à l’échelle
	-	Couverture géographique

Contraintes :
	-	Exposé sur Internet
	-	Sécurisation
	-	Confiance envers le fournisseur

Cloud + agile ==> Les développeurs qui ont le pouvoirs

Les Datas sont les informations les plus importantes

46% des applications contiennent des failles critiques

> OWASP :communauté qui liste des failles critiques

Plateforme sécurité :
	-	Web Application firewall
	-	VNP
	-	Multi Facteur authentication
	-	Traffic monitoring

Analyse des risques : les 4 piliers
	-	Disponibilité
	-	Intégrité
	-	Confidentialité
	-	Traçabilité
==> tous les stackholders + dev sont présents dans ce meeting.

On évalues les risques sur 2 axes :
	-	la probabilité d’occurence
	-	L’impact
==> On transforme ces risques en US
==> Rajouter les risques dans le backlog
==> Le PO est responsable sur la sécurité comme les IT

4 environnements :
	-	Local
	-	DEV
	-	QA
	-	PROD

Peer review entre équipe
	-	DAST / SAST : outils automatiques
	-	Test de pénétrations

<img src="{{ site.url }}/images/2019-04-19-devoxx/delivery-pipeline.png">

SAST :
	-	Static application security testing
	-	Analyse du code source
	-	Feedback rapide sur les erreurs reliées à la sécurité
	-	Performant
	-	Couteux pour la mise en place / configuration des rules
	-	Permet de valider les différentes surfaces d'attaques
	-	Entrée des users
	-	WS API
	-	90% des API ont du code mort
	-	Système de fichier
	-	BDD
	-	Vérifie des dépendances

DAST : Dynamic aplication Security Testing
	-	trouver les vulnérabilités sur un code qui tourne (le role du hacker)
	-	Tester la config du serveur


Il faut avoir les 2 outils : DAST & SAST

Misconfiguration
	-	Dans le cas d’une mauvaise configuration
	-	Utilisation des comptes par défaut
	-	Port ouverts
	-	Patch de sécurité non appliqué
	-	Reverse proxy

XSS : Cross type scripting
	-	attaque le navigateur des users

=> La sécurité ne se limite pas aux outils

Les tests de pénétrations
	-	Red team & Blue team
	-	Bleu :
	-	sécurise l’application
	-	Monitore l’application
	-	Red Team
	-	Attaquant qui essaie de casser l’application
	-	Broken Authentication
	-	Use Multi factor authentification
	-	Ban
	-	Bug bounty
	-	On paye un entreprise extérieure qui est payé à chaque vulnérabilité trouvée
	-	Extérieur pour avoir un oeil externe sur ce qui est fait

La sécurité s’adapte à l’agilité
	-	création de la valeur
	-	Adapter la sécurité au besoins
	-	Sécurité outillée
	-	La sécurité tout au long du projet
	-	responsabilisation des équipes
	-	Test & learn


<img src="{{ site.url }}/images/2019-04-19-devoxx/couverture-awasp.png">
