---
layout: post
title:  "Dev Env for the Elixir Beacon in Spring Boot"
date:   2018-11-11 09:34:28 +0800
categories: elixir beacon ga4gh java spring-boot intellij bioinformatics
---

**Background**
[Elixir Beacon][elixir-beacon] is a bioinformatics microservice which allows to query a 
genome variant-calling dataset for the absence or presence of a specific variant. Such a 
service is of interest as it can provide anonymized information from sensitive datasets.
Beacon is also part and has been chosen as a driver project of the [*Global Alliance for Genomics and Health*][ga-4-gh].

**Cloning, importing...**
The project repository is available on the [github][elixir-github]. I use currently IntelliJ as IDE with Spring plugin. In IntelliJ, first project import is selected. Beacon uses Maven as build system. It is split into a core module and an application specific implementation module. Further, also a test module. On the *Project Import* screen, the option for recursive project file search should be activated, then all three modules are found and imported.

With the Spring Plug-in for installed, IntelliJ will automatically recognize the projects as such and setup the build and run configuration accordingly.

**Providing the Database**
The main task to get the setup running is to provide the database. The easiest case is to use the dockerized PostgreSQL setup provided by within the [Beacon Elixir UI][beacon-elixir-ui] repository.  





[elixir-beacon]: https://beacon-project.io/
[ga-4-gh]: https://www.ga4gh.org/
[elixir-github]: https://github.com/ga4gh-beacon/beacon-elixir
[beacon-elixir-ui]: https://github.com/ga4gh-beacon/beacon-ui-elixir


