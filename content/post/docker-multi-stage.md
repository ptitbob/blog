+++
date = "2017-06-18T16:50:16+02:00"
description = "Petit exemple d'utilisation du build multi-stage pour une application Spring"
draft = true
title = "Docker multi-stage"
tags = ["docker", "spring", "java"]
words = ["docker", "multi-stage", "build"]
metadescription = "Exemple de création d'image via la fonctionalité multi-stage de docker d'une application Spring/java avec build maven et injection dans un container léger basé sur une distri Alpine"
+++

Depuis la version 17.05, Docker propose une nouvelle fonctionnalité : le build Multistage, ce qui rendra le build d'application plus attrayant et lisible.

Le but sera de contruire une application via Maven ([image de taille imposante](https://hub.docker.com/_/maven/) : 620 Mo), puis de construire une image légère ne contenant que le JRE (image légère : 81,4 Mo sans l'application - [OpenJDK ne contenant que le JRE et basé sur alpine](https://hub.docker.com/_/openjdk/)).

## L'application

L'application est juste le support à cet exemple, les sources sont disponible au niveau du repos [github :: ptitbob/Dockerfile-multi-stage_server](https://github.com/ptitbob/Dockerfile-multi-stage_server).

Elle expose 2 méthodes, qui renvoient soit pong, soit le numéro de version.
Que vous pouvez atteindre via cURL (`curl -i localhost:8080/ping` & `curl -i localhost:8080/ping/version`) ou par [HTTPie](https://httpie.org/) (`http :8080/ping` & `http :8080/ping/version`)

Le repo possède 2 branches et pour la branche principale 2 tags de version qui seront utilisés pour les deux exemples sur le build multistage.

## Dockerfile lié aux sources

La première des solutions est d'intégrer le `Dockerfile` directement aux sources. C'est le cas de la [branche *`docker`* des sources de l'application](https://github.com/ptitbob/Dockerfile-multi-stage_server/tree/docker).

Le `Dockerfile` est constitué de deux partie ([source complet du Dockerfile](https://gist.github.com/ptitbob/39aaa935a0725ece5706484f36fff8d2)) : 

La première permet le build :

{{< gist ptitbob 12f7beefec4e1cb9a958d96b67a242e5 >}}

* On fixe l'image d'origine, et on nomme cette étape *builder* (`... as builder`)
* Nous nous plaçons dans le repertoire de travail.
* On copie le fichier projet maven puis les sources.
* On lance via un `RUN` le packaging du projet.

Le seconde partie...

{{< gist ptitbob 9a9034bef446e86ee6bcd0de12e3cb8e >}}

... va permettre de créer l'image finale de notre application.

* On prend comme base l'image contenant le JRE et on se place dans le repertoire `root`
* **On copie depuis l'image temporaire qui a servi à faire le build le jar créé dans le repertoire root de l'image**
* et le plus simplement du monde, on déclare la commande de lancement de l'application.


Pour lancer le build, rien de plus simple : 

```bash
docker build -t shipstone/multistage:0.9.0-docker .
```

Une fois le build executé, vous pouvez voir l'image créée ainsi que l'image anonyme ayant servi à construire l'application : 

```bash
[~]$ docker image ls
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
shipstone/multistage              0.9.0-docker        fbeb9b0781da        31 seconds ago      95.9MB
<none>                            <none>              b85a5ce074da        34 seconds ago      635MB
openjdk                           8u131-jre-alpine    58ce9579eac6        2 weeks ago         81.4MB
maven                             3.5.0-jdk-8         66091267e43d        4 weeks ago         620MB
```

Vous pouvez supprimer l'image anonyme via un petit **`docker image prune`** *des familles*.
On remarque aussi que l'image créée fait la taille de l'image du jre plus la taille de l'application.
La taille de l'image maven n'apparait pas :)

Cependant, cela pose un petit problème : le fichier Dockerfile est lié au sources, ce qui n'est pas satisfaisant et surtout sujet à des erreurs de manipulations.
Une solution simple existe, créer un `Dockerfile` excentré.

## Dockerfile séparé

La solution que j'ai implémenté est simple, mon nouveau Dockerfile, hors des sources, télécharge les sources de l'application depuis le repo git ( *qui est présent par défaut dans toutes les bonne distri ;)* ).
Et histoire d'être le plus fléxible possible, je variabilise la version.
Version qui est symbolisé dans mon [application (branche princiale)](https://github.com/ptitbob/Dockerfile-multi-stage_server/tree/master) par des tags (1.0.0 & 1.1.0).

Le `Dockerfile` est sensiblement le même que précédement, il est exposé dans le repo [github :: ptitbob/Dockerfile-multi-stage_dockerfile](https://github.com/ptitbob/Dockerfile-multi-stage_dockerfile) :

{{< gist ptitbob e49035dd22099813594f35a18251c7bc >}}

Il prends en argument le nom du tag de version (*Je vous l'accorde, on peut aller nettement plus loin*)

Je déclare une variable de ligne de comande Docker `version`

**`ARG version`** qui pourra être appelé dans la ligne de commande: **`--build-arg version=1.0.0`**


Je fixe une variable d'envirronement avec la version passé en paramètre, et si elle n'est pas fixée elle prendra alors la valeur `1.0.0` 

`ENV version ${version:-1.0.0}`

Il ne me reste plus qu'a cloner le tag et lancer ensuite le build de l'application cloné dans le repertoire.

Comme je n'ai pas configuré de nommage standard pour l'application, le fichier executable est versionné, et donc je vais redéclarer une variable de build et d'envirronement comme pour l'image de build, sauf que je construits le nom du fichier à recupérer depuis l'image de build.
*That'a all !!*

Pour lancer le build (*penser à bien nommer vos tag*) : 

```bash
$ docker build -t shipstone/multistage:1.0.0 -t shipstone/multistage:latest .
```

***équivalent à*** :

```bash
docker build --build-arg version=1.0.0 -t shipstone/multistage:1.0.0 -t shipstone/multistage:latest .
```

Et ensuite pour la version 1.1.0 :

```bash
docker build --build-arg version=1.1.0 -t shipstone/multistage:1.1.0 -t shipstone/multistage:latest .
```

Je construit volontairement deux tag d'image (qui référence en réalité la même image), le tag de version de mon application et le tag de dernier build (latest).

Maintenant j'ai comme images (j'ai fait un `docker image prune` avant histoire de supprimer les images de build) : 

```bash
docker image ls
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
shipstone/multistage              1.1.0               f00b35c1f6e6        15 seconds ago      95.9MB
shipstone/multistage              latest              f00b35c1f6e6        15 seconds ago      95.9MB
shipstone/multistage              1.0.0               7ea6490f40f4        21 minutes ago      95.9MB
shipstone/multistage              0.9.0-docker        fbeb9b0781da        23 hours ago        95.9MB
openjdk                           8u131-jre-alpine    58ce9579eac6        2 weeks ago         81.4MB
maven                             3.5.0-jdk-8         66091267e43d        4 weeks ago         620MB
```

Et voilà, vous avez un `Dockerfile` générique pour votre application, afin de la builder et la rendre disponible empacké gentiment dans un image basé sur une distri légère.

Pour créer un container basé sur la dernière image et l'éxécuter, simple :

```bash
docker run -p 8080:8080 --name multistage shipstone/multistage:latest
```

*équivalent à* :

```bash
docker run -p 8080:8080 --name multistage shipstone/multistage:1.1.0
```

> On pourrait aller beaucoup plus loin dans la généricité/standardisation du build via un petit script shell ou python, mais ce n'était pas l'objet de mon exemple
