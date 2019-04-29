---
layout: post
title: "DevoxxFR 2019 - Modern Java"
date: 2019-04-18
excerpt: "Modern Java"
tags: DevoxxFR conferences java
feature: /images/2019-04-19-devoxx/2019-04-19-devoxx-intro-affiche.png
comments: false
---

Talk de __Mark Reinhold__ @mreinhold


# Abstract
In the past two years, we changed Java in three ways that we never have before: We modularized the platform, we removed some components, and we accelerated the pace of new releases. These changes aim to keep Java vibrant in an ever-changing world of competing platforms and new styles of application deployment, whether to the cloud or to app stores. We’ll see why these changes are not as disruptive as you may think, and demonstrate some of the recent and future additions to the language and the platform.

# Notes

The change is the only constant

Many other plateforms
Move faster
Deployed in different ways

Module exported only used packages —> security

> Do not use JDK internals

__Jdeps__ : Detect dependencies used / internal API used

__JDK 11__ :
- Remove java EE and Corba
- Dynamic class-file constants

Java Choose:
- LTS every 3 years
- Little update every 6 Months

> Constantly evolving : Every thing is chasing and nothing stands still - Heracultis

## Evolutions
- Developer productivity
- Program performance
- Amber : Right sizing language ceremony
  - Express clearly what you mean
- Java 12 :
  - Switch avec des lambdas
  <img src="{{ site.url }}/images/2019-04-19-devoxx/enum.png">

  - String usr plusieurs lignes (java 13)
  <img src="{{ site.url }}/images/2019-04-19-devoxx/strings.png">

  - Record pour replacer getter/ setters / to string / equals / toString

<img src="{{ site.url }}/images/2019-04-19-devoxx/record.png">

- Loom : Fibers
  - Type inference
  - var for local fields
- Fiber.schedule ….
- Changer de contexte de thread rapidement
- Valhalla : ValueTypes & specialized. Generics

More details on [Java Keeps throttling](https://www.slideshare.net/mobile/jpaumard/java-keeps-throttling-up).
