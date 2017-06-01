+++
date = "2017-05-22T18:14:02+02:00"
description = "Exemple d'initialisation d'une base de données postgreSQL inclus dans un container"
draft = false
tags = ["docker"]
title = "Docker, Postgresql et initialisation"
words = ["docker", "postgres", "postgresql"]
metadescription = "Exemple d'initialisation d'une base de données postgreSQL inclus dans un container"
+++

Qui n'a pas eu besoin un jour ou un autre d'une base de données déjà initialisée ?

Personnellement, depuis que j'ai découvert docker, je n'installe plus de serveur de base de donnée sur mon poste, je passe par un container.
Ce qui me permets d'isoler mes données.
Mais un problème que j'avais était d'initialiser mes données à la création de l'image.

Voici un exemple initialisant une base [Postgresql](https://www.postgresql.org/) à la construction de l'image, c'est à dire créant la structure et initilisant les tables avec des données injectées.

Le principe est simple, il s'agit de copier les fichiers d'initialisation de la base de donnée dans le repertoire d'init de l'image [Postgresql](https://hub.docker.com/_/postgres/).
Tout se passe dans le fichier `Dockerfile` : 

{{< gist ptitbob fd00da53d2144038c0e768e82f8f2df4 >}}

Tous mes fichiers d'initialisation se trouvent dans le sous répertoire `sql`, ce qu'il faut savoir, c'est que le processus d'initialisation de postgresql va les interpreter dans l'ordre alphabétique, donc voici ma structure : 

| Nom | contenu |
|-----|---------|
| `00_initialization.sql` | Initialisation de la base (schéma, autorisation, ect) |
| `10_structure.sql` | Structure de la base (table, trigger, etc...) |

Le reste des fichiers (`20_regions.sql`, `25_districts.sql`, `30_cities.sql`) contiennent les scripts d'insertion des données (*en l'occurence, la liste des régions, département et leur chef-lieu et autres ville d'importance*).

Vous pouvez trouver les sources de cet exemple dans mon [repository github](https://github.com/ptitbob/docker_postgresql_initialization).

Pour tester cet exemple, lancez le fichier `docher-compose` se trouvant la racine : 

**`docker-compose -f docker-compose.postgres.yml up`**

Ensuite vous pourrez vous connecter à la base ainsi initialisée via [pgAdmin](https://www.pgadmin.org/) (par exemple)

| champs | valeur |
|--------|--------|
| host | `localhost` |
| port | `5432` |
| Maintenance database | `postgresdb` |
| User name | `postgres` |
| password | `postgres` |

> Enjoy it !!
