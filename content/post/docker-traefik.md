+++
date = "2017-01-25T00:21:22+01:00"
description = "Mécanisme de découvete des container par Traefik"
title = "Docker, Traefik et les reseaux"
draft = false
tags = [ "Docker", "Traefik" ]
metadescription = "Comprendre la decouverte des container par Traefik et son mécanisme de liaison avec l'envirronement Docker. Le tout soupoudré d'un debut de commencement d'explication du fonctionnement des reseaux docker"
words = [ "Docker", "Traefik", "network", "reseau" ]
+++

Intégration Docker API / Treafik avec gestion des domaines/sous domaine

> Ce "hands-on" est parti d'une problématique rencontré avec [Sébastien Laporte](https://github.com/seblaporte) sur la mise en production d'une serie d'application derrière un traefik sans interruption de service de reverse-proxy.

*Nous utilisons la version 1.12 de docker, cet article aura une mise à jour pour intégrer les nouveautés de Docker 1.13*

Le code source de cet exemple est disponible ici : [github :: ptitbob/docker-traefik](https://github.com/ptitbob/docker-traefik)

Le but de ce petit projet est de montrer l'intégration de [Traefik](https://traefik.io/) et de son interaction avec l'API docker. 
Cette interaction permet la prise en compte des container monté en tant que route au sein du reverse proxy qu'est Traefik à l'aide des labels docker-compose.
***Et surtout si tous les container, Traefik et les container branché sur Traefik sont au sein d'un même reseau*** ce qui permet de ne pas linker Traefik au nouveau container, et donc de ne pas arreter le reverse-proxy pour une prise en compte.

> Les labels de compose permettent l'exposition de pseudo propriété qui peuvent être utilisées par des service tiers, dans notre cas Treafik. 
>
> Mais nous verrons cela plus loin.

Nous étion parti sur le principe de créer un reseau via le fichier descriptif docker-compose.
Mais il s'est avéré que docker-compose va créer un réseau en le préfixant avec le nom du repertoire ou se trouve le fichier.
Ce prefixage peut être modéré en fixant un nom de projet comme cela (```-p``` indique un nom de projet) :

```
docker-compose -p shipstone -f traefik/docker-compose.traefik.yml up
```
Mais au final, cette méthode s'est révélé peu élégante, et a mis en lumière notre non compréhénsion de la philosophie du docker-compose...
Et surtout c'était peu satisfaisant, car cela oblige à marquer d'un même projet des descriptif compose qui ne font justement pas partie d'un même projet !

Il faut le reconnaitre, c'est surtout une non connaissance du fonctionnement du principe de reseau au sein de docker qui m'a induit en erreur.

Alors, partant d'une page blanche, voici pour nous, la meilleure manière de gérer la mise en exposition de site de manière automatique dans le contexte d'un deploiement au sein de docker.

La premiere étape de créer un reseau indépendament de tout (on le nommera ```shipstone_net```) en utilisant le CLI de gestion des reseaux de docker : 
```
$ docker network create shipstone_net
e0d6ecb92f898f3422e3c7e0729ab86d4d9c2f0a446a203338b538a9cdc946b4
```

On peut donc verifier la création du réseau : 
```
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
b8f5bc540db4        bridge              bridge              local
72c5dfdf71bf        host                host                local
e0d6ecb92f89        shipstone_net       bridge              local
```
Maintenant il ne reste plus qu'a le déclarer comme réseau "externe" au niveau de chaque fichier ```docker-compose.yml``` : 

{{< gist ptitbob 7f9c982068bf79e2424baf4b8fa83803 >}}


et de déclarer le rattachement au niveau de chaque container (dans le cas de notre container Taefik, par exemple) : 
{{< gist ptitbob 0be8b2303985c47ff138af89aadb6d73 >}}

Rattachement qui se fait via la section ```networks``` des descriptifs "docker-compose". Et il faut faire cela pour chaque container.

Décrivons un peu notre cas de test. 
Il s'agit d'exposer un domaine (```shipstone.local```) et deux sous domaines (```web1.shipstone.local``` et ```web2.shipstone.local```). 
Chaque sous domaine ayant 2 backend (2 nGinx en load balancing). 
Pour simuler cela, j'ai juste modifié mon fichier host afin d'ajouter des URL pointant sur mon localhost. 
Je vous donne une petite astuce au passage (j'ai rencontré le problème sur OSX), si la carte reseau de votre PC prend en charge l'IPv6, la seule définition de la liaison avec l'IP localhost en IPv4 (```127.0.0.1```) entraine une latence pas possible. 
L'astuce consiste à déclarer un double mapping (IPv4 & IPv6) : 
```
# shipstone local
fe80::1%lo0	shipstone.local
127.0.0.1	shipstone.local
fe80::1%lo0   web1.shipstone.local
127.0.0.1   web1.shipstone.local
fe80::1%lo0   web2.shipstone.local
127.0.0.1   web2.shipstone.local
```

Maintenant, il faut déclarer l'adhérence de Traefik à l'API Docker (dans le fichier ```traefik.toml```) :
```toml
[docker]
  endpoint = "unix://var/run/docker.sock"
  domain = "shipstone.local"
  watch = true
``` 
Vous remarquerez qu'il écoute un socket UNIX (que les barbus de passage me pardonne ma non connaissance du fonctionnement d'un UNIX en ce qui concerne les écoutes sur socket).
Dans mon cas, visiblement docker ne créé pas cette liaison, c'est pourquoi je l'ai rajouté en tant que volume mappé dans le compose de mon Traefik (cf. plus haut).

Vous noterez aussi que je défini un domaine par défaut (```shipstone.local```), ce qui nous permettra d'acceder à la console de Traefik [http://shipstone.local:8080](http://shipstone.local:8080).

Maintenant lançons notre compose pour Traefik (sans prefixage par un nom de projet) : 
```
docker-compose -f traefik/docker-compose.traefik.yml up
```
Une fois cela fait, vous pouvez ouvrir la console Traefik, et voir qu'une seule route a été créer en mappant le domaine de base sur le container de Traefik.

Maintenant, comment configurer Traefik pour que les containers décrits dans mes deux fichiers compose (```docker-compose.front1.yml``` et ```docker-compose.front2.yml```) soit pris en compte ?

L'astuce reside dans le fait de decaler les informations de configuration du reverse-proxy de chaque container au niveau des labels exposé par ceux-ci et que Traefik pourra interpreter via sa liaison sur l'API Docker.
Les labels devront être prefixé par ```traefik``` et décrire ce que l'on trouve d'habitude dans le fichier de configuration de Traefik. Exemple pour le serveur 1 du Front 1 pour le sous domaine ```web1.shipstone.local``` :
```toml
        labels:
            - "traefik.backend=front_http_1"
            - "traefik.frontend.rule=Host:web1.shipstone.local"
            - "traefik.port=80"
            - "traefik.backend.loadbalancer.method=wrr"
            - "traefik.backend.loadbalancer.sticky=false"
            - "traefik.protocol=http"
            - "traefik.weight=10"
``` 
Nous y configurons (dans l'ordre d'apparition) : 

* le nom du serveur back
* la route du front, le sous domaine, mais on peut aussi ajouter d'autre règle (voir même faire de la récriture d'URL, mais cela fera l'objet d'un autre exemple)
* le port exposé
* La méthode de load-balancing, juste informatif, car elle porte une valeur par défaut ```wrr```
* Si on reste adhérent à une IP (session), même chose je ne l'ai mis qu'a titre indicatif, car je le change pas la valeur par défaut.
* On fixe le protocole utilisé - *Petit rappel : Traefik joue avec de l'HTTP2, ce qui veux dire que par défaut, il sert du https (port 443)*.
* Le poid du backend, utilisé pour le load balancing.

Maintenant, on peut lancer les container de nos fronts : 

```
docker-compose -f front/docker-compose.front1.yml up
```
et
```
docker-compose -f front/docker-compose.front2.yml up
```

Et si vous regardez dans le console Traefik, vous les verrez apparaitre ! *C'est de la magie ? Non, c'est Traefik et Docker*

Vous pouvez tester l'exposition (j'utilise [```httpstat```](https://github.com/reorx/httpstat) pour visualiser le timelaps) : 

```
$ httpstat web2.shipstone.local
Connected to 127.0.0.1:80 from 127.0.0.1:59866

HTTP/1.1 200 OK
Accept-Ranges: bytes
Cache-Control: max-age=0
Content-Length: 1002
Content-Type: text/html
Date: Thu, 15 Dec 2016 21:57:34 GMT
Etag: "5851c79c-3ea"
Expires: Thu, 15 Dec 2016 21:57:34 GMT
Last-Modified: Wed, 14 Dec 2016 22:28:44 GMT
Server: nginx/1.11.6
Set-Cookie: _TRAEFIK_BACKEND=http://172.18.0.6:80

Body stored in: /var/folders/h8/0qx72_294gjfxsjcky4nw74m0000gn/T/tmpxrReof

  DNS Lookup   TCP Connection   Server Processing   Content Transfer
[     5ms    |       1ms      |       10ms        |        1ms       ]
             |                |                   |                  |
    namelookup:5ms            |                   |                  |
                        connect:6ms               |                  |
                                      starttransfer:16ms             |
                                                                 total:17ms
```

Ça fonctionne, tout simplement. Vous noterez au passage que Traefik y laisse sa patte, ***pensez à supprimer cette possibilité en prod***

***Have fun***
