+++
date = "2016-05-11T20:27:53+02:00"
description = "*Retour sur 3 jours de conférence*"
metadesctiption = "Retour sur les trois jours de Devoxx France 2016"
title = "Devoxx France 2016"
words = [ "Devoxx", "Devoxx France 2016", "jug", "ApsideTOP" ]
tags = [ "Devoxx", "JUG"]
+++

![Logo Devoxx France](/images/post/devoxx/devoxx_fr_2016_banner_900_230.png)

*Juste un petit retour sur la conférence* ***Devoxx France*** *qui s'est déroulé du 20 au 22 avril.*

```Update du 14/05/2016 : On m'a soufflé que j'avais oublié de parler des Keynotes... (en bas)```

Pendant 3 jours j'ai pu assister à des conférences, universités sur notre passion (et accessoirement, notre métier). Et j'avais juste envie de donner mon opinion sur certaines d'entre elles, de partager ce que j'ai appris ou tout simplement donner mon avis. :)

Je n'aborderai pas forcement toutes les conférences et universités dans un ordre particulier, ou plutôt si, celui de ma préférence.

A la fin de l'article (*le fourbe, il a fait ça pour nous forcer à tout lire*) je vous mets quelques liens pour la chaine youtube de Devoxx France et le flipboard que je me suis amusé à réaliser avec les liens vers les slides que j'ai pu trouver sur la toile.

Alors commençons...

## Show time baby !!! Containers & VM by Quentin !

Certainement une des prez qui m'a le plus passionné, technique, speed et marrante. Le conferencier y est pour beaucoup, j'ai nommé Quentin Adam ! Qui nous avait régalé avec une [keynote sur le Cloud l'année dernière](https://www.parleys.com/tutorial/the-end-of-server-management) (en faisant une analogie sur l'emploi de l'electricité au siècle dernier dans les entreprises - J'avoue l'avoir diffusé autours de moi, je ne pouvais pas ne pas le faire).

Comme toujours Quentin nous fait un show extraordinaire ou les explications technique pointues et petites piques bien senties se croisaient. ***Que du plaisir*** !!!

Beaucoup d'informations, qui vont me faire re-regarder cette présentation que je vous conseille fortement : [Containers, VMs... Comment ces technologies fonctionnent et comment les différencier ?](https://youtu.be/wG4_JQXvZIc)...

J'ai compris pourquoi [clever-cloud](https://www.clever-cloud.com) (la boite de Quentin, en passant) ne présente Docker qu'en support beta :) - J'avais eu l'occasion d'aborder ce point avec eux avant la conférence lors de mon passage sur leurs stands. Des gars géniaux. Si vous en avez l'occasion et le besoin, allez tester Clever-Cloud, perso je suis convaincu.

Alpine, la distri phare et star du monde Docker, s'en prends 2/3 derrières les oreilles, ce fut très rafraichissant... :D

En gros, une remise en perspective et une mise au point salutaire.

Il a aussi animé une université, que je n'ai pas suivi, mais que je vais regarder dès que j'aurais 3h de libre :)

## Le design d'API REST, un débat sans fin ? *par Guillaume Laforge*

Guillaume a décider d'aborder ce débat qui est certainement sans fin effectivement, et même si je ne suis pas complément d'accord avec lui sur certains point abordés. Ne parlait t'il pas de débat sans fin justement, autours d'une bière...

Il a présenté tout d'abord les bases exposées par Roy Fiedling dans son mémoire (*[Principled design of the modern Web architecture](https://www.ics.uci.edu/~fielding/pubs/webarch_icse2000.pdf)*), soft & easy, les rôles des verbes HTTP, qui bien souvent sont ignoré ou mal interprété dans nos boites ou nous devons précher parfois la bonne parole.

Je propose de vous les rappeler maintenant, ça peut pas faire de mal :

Verbes | `http://www.site.com/person/` | `http://www.site.com/person/123`
------ | ----------------------------- | --------------------------------
**GET** | liste des personnes | acces à une personne
**POST** | Création d'une personne | *Rien...*
**PUT** | Remplace la liste complète | Met à jour une personne
**DELETE** | Efface toute la liste des personne | efface la personne identifié dans le path

Et ***paf*** c'est là ou je ne suis pas d'accord avec Guillaume (et visiblement avec une bonne partie de la salle), ils nous a présenté la manière de créer les URI, c-à-d l'accès aux ressources. Au delà du fait qu'il faut préférer des noms (et là je ne le contredirai pas, bien au contraire), mais plutôt de préférer utiliser le pluriel à la place du singulier... *Personnellement je préfère utiliser le singulier, pour une histoire de normalisation...*

Prenons mon exemple, person, en anglais (eh oui, pour moi une URI doit être en anglais...), le pluriel peut être persons ou people, ce qui pour moi pourrai porter préjudice à la lecture de l'API. Son argument tient la route, on accède bien à *la personne d'id 123 de la liste de personnes*. Mais globalement, on fait plus d'opération sur une ressource identifiée que sur la liste. *Mais ce fut le seul cas ou je n'ai pas été d'accord avec son analyse*.

Mais il a aussi abordé des problèmes liés au design d'API web comme le transfert d'une liste, wrapper ou pas ? Personnellement je suis d'accord avec lui, utiliser les champs du header pour des informations complémentaires (pour la pagination) et le body ne contenant que la liste, avec cependant des exceptions.

Nous avons aussi aborder la gestion de la pagination, du cache, normalisation des codes HTTP.

Enfin plein de bonne pratique, qu'il parait intéressant de regarder.

Je vous laisse regarder la [présentation youtube de Guillaume à Devoxx](https://youtu.be/6onHFNqMUwY), et aussi ses [slides](https://speakerdeck.com/glaforge/the-never-ending-rest-api-design-debate-devoxx-france-2016).

## Le show Remi Forax

Remi est l'un des conférencier que j'apprécie le plus, je l'ai découvert en "*live*" lors d'une édition précédente de Devoxx, et cette année non plus je n'ai pas été déçu bien que son université sur les lambdas donnait un peu mal à la tête :D.

La première conférence portait sur la concaténation des chaines dans Java, avec beaucoup de bytecode et une explication allant au fond des choses de Java 8 à Java 9. Très instructif et surtout une exploration de la "compilation" java.

Je vous laisse regarder la prez : [String Concatenation de 1 à 9](https://youtu.be/U97zT8kA7G4) c'est très instructif et passionnant.

La seconde conférence abordait un des points majeur de Java 9, j'ai nommé le très attendu ***Jigsaw***.

Rémi nous l'a presenté dans son style inimitable ! Ou il a abordé tout d'abord les problèmes de la plateforme java actuelle, connu sous le nom de ```jar hell```. Et surtout ce que pourra nous apporter Jigsaw (ou pas) et comment cette API sera implémentée pour nous qui aurions a l'utiliser tous les jours lors de nos tentative de ***Jigsawification*** (© Rémi Forax :D ).

Revenir sur la conf, serai prétencieux et surtout source d'erreurs de ma part :) :) :) Je vous laisse donc regarder sa présentation : [Jigsaw est là pour nous sauver](https://youtu.be/a8xnnR3Bz6w).

## Le web et Hubert

J'ai aussi suivi deux conférences d'Hubert Sablonière qui vous a parlé de Flexbox de la facilité enfin atteinte pour nous pauvre développeurs (eh oui, je le revendique, je ne suis pas web design, ni full stack). Conférence dynamique avec des slides interactive, je vous invite a regader sa conf : [Flexbox, et le CSS redevient fun !](https://youtu.be/5F_ngjHDcJQ), j'avais déjà suivi une conférence équivalente, et cela m'a confirmé que Flexbox nous sauvera en ce qui concerne le placement de nos éléments dans les applications web qui consomme nos ressources issues de nos super API REST ;) ... (*juste en passant, histoire de tester flexbox de manière fun, j'avais dégoté un petit tower defense ou la résolution passait par du code CSS flexbox : [http://www.flexboxdefense.com](http://www.flexboxdefense.com) - marrant et prenant).

Il nous a aussi parlé des JSON Web Token aka [JWT](https://jwt.io), utilisé dans le domaine des identification et authentification. Cela a permis de remettre dans le contexte et de clarifier leur utilisation. Vous pouvez visualiser sur youtube sa presentation : [100% Stateless avec JWT](https://youtu.be/A2-YImhNVMU).

## Pourquoi Maurice ne doit pas faire du Go

Jean-Laurent de Morhlon, a presenté Maurice...

... Ah non, plutôt son retour sur son experience de codeur majoritairement javaiste dans le monde de go et des gopher :)

Je voulais suivre cette conf, car je commence aussi à jouer de mon côté avec le langage, la nuit et pas à son niveau (il est entré chez docker et a laissé une partie de son héritage dans le code de docker machine, bien que maintenant il joue plus avec du C# pour réaliser le client Docker windows).

Loin des tutoriaux, il est revenu sur ce qu'il faudrait savoir avant d'aborder ce langage. Et en gros, le letmotiv est celui-ci :

> ***c'est comme ça...*** (ne lutte pas)

A travers quelques petits exemples et autres anecdoctes il reviens sur son experience et nous présente le langage Go.

Pour revenir à la conf, j'ai entendu un peu ce que j'ai rencontré pendant mes première experiences.

Je me suis beaucoup amusé et aussi appris un peu.

Ce qu'il faut retenir, c'est que Go est un langage système de bas niveau aussi capable d'exposer un serveur en 4 lignes (ou presque) qui compile en natif, et là avec le gros avantage, c'est que vous pouvez compiler pour plusieurs architectures et OS sans changer de machine, ni même lancer de machine virtuelle seulement avec 2 attributs en ligne de commande sur l'executable de build (je ferai peut être un billet sur le développement en go plus tard).

Et là s'arrete les bonnes nouvelles :D

Oubliez les variables et autre package aux nom significatif, d'ailleurs il commence par utiliser le package ```fmt``` le bien nommé :D.

Et surtout, oubliez la gestion des erreurs à l'aide d'exceptions que bullent et que nous pouvons trapper...

Autre chose, il n'existe pas d'IDE (hors plugin)

Mais bon, c'est aussi un langage qui permet pas mal de chose avec sa capacité d'être système et de permettre des trucs marrant. Je vous laisse découvrir sa prez et surtout tester [go(lang)](https://golang.org), c'est franchement marrant.

Et quelques liens :

* Pour les demo de live-coding, Jean-Laurent utilise Atom avec le plugin [Go-Plus](https://github.com/joefitzgerald/go-plus) (pas mal du tout)
* [La vidéo de la conférence](https://youtu.be/LIFZPzupwgs)

## Spring Boot 1.3 pour le web

Une université sur spring boot (1.3) animé par Brian Clozel et Stéphane Nicoll, j'avais déjà suivi celle de l'année dernière... Pourquoi suivre celle-là alors me direz vous ? J'avoue que sur le moment j'avais apprecié celle de l'année dernière et je voulais continuer en mode soft cette année...

Je n'ai pas été déçu, ce fut un approffondissement celle de l'année dernière, donc que du bon... Donc leur fils d'ariane a ete de créer une application web tout au long de ces 2h40 qu'a durée cette université.

J'ai pris beaucoup de plaisir à les écouter (comme l'année dernière) et je leur doit quelques unes de mes nuits blanches passées et à venir...

Si le sujet vous interesse, je vous conseille de regarder ces deux conférences :

* [Devoxx France 2015 : De zéro à héros avec Spring Boot (parleys.com)](https://www.parleys.com/play/de-zero-a-heros-avec-spring-boot)
* [Devoxx France 2016 : Spring Boot 1.3 pour le web (youtube)](https://youtu.be/sR8PyhJa-Zw)

## Quoi d'neuf Docker - live Docker sur ARM

Là j'avoue que ce fut pour le fun... Pas que je n'aime pas docker , ni les bidouille sur rPi et encore moins la possibilité de faire tourner docker sur un truc gros comme une paquet de clops, bien au contraire... C'est que c'était un passage [Quoi d'neuf Docker ?](https://www.youtube.com/channel/UCOAhkxpryr_BKybt9wIw-NQ) avec Nicolas de Loof et Christophe Labouisse.

Donc tout au long de la demonstration d'un cluster de docker monté sur 4 rPi... Mais ce qui suis, je ne pas en dire plus afin de ne pas spoilier... Je vous laisse regarder la video et vous regaler : [Quoi d'neuf Docker - live](https://youtu.be/EQNj5GBGg9Y).

Ce fut rafraichissant, même si je pense que certains gilets rouges ont du avoir quelques sueurs froide :D

## Devoxx4Kids : Faire découvrir la programmation aux enfants

D. De Luca, A. Neveu, E. Feller et X. Bourguignon nous ont presenté les outils utilisé pour cette super initiative **[Devoxx4Kids](http://www.devoxx4kids.org/france/)**.

![Logo Devoxx4Kids](/images/post/devoxx/DevoxxWithoutBrackets_PNG_1000px-636x223.png)

Tout long de celle-ci nous sommes passé des petit aux plus grand à travers différents projets et actvités proposer à nos enfants lors des différentes manifestations. :)

Je vous lance en vrac mes notes et vous laisse regarder la [vidéo passionnante de cette conférence sur youtube](https://youtu.be/T_nrD3E5qD0). :)

### Primo Cubetto

*4-6 ans*

Piloter un robot avec des forme de couleur, afin d'appréhender la notion de séquentialité dans les ordres que nous pouvons donner à un "cubetto" afin d'atteindre un point donné, le tout avec un jeu de forme et de couleur. Pas mal du tout. Vous pourrez trouver ce petit trésor sur kickstarter : [Cubetto - Hands on coding for ages 3 and up](https://www.kickstarter.com/projects/primotoys/cubetto-hands-on-coding-for-girls-and-boys-aged-3).

### Lego mindstorm

*a partir de 10 ans*

Par contre, le prix fait mal : 400 € pour la version de base.

### Arduino

Utilisation d'un Arduino pour aborder l'electronique. J'ai été interressé sur pourquoi un arduino plutôt qu'un rasberryPi ? La réponse a été, car c'est plus simple :) et plus à même de mettre en place l'initiation à l'électronique (car c'est un controlleur) comparé au rPi (qui lui est un ordinateur). Bon, ben maintenant, il n'y plus qu'a ...

### Scratch

Utilisation du leap motion avec Scratch (neccessite l'ajout lib pour intégrer les mvt du leapmotion) pour que les enfants puisse programmer pour interagir avec ds objets réels : leurs mains !

### MakeyMakey ou comment utiliser les objets de tous les jours...

Vous avez certainement vu passé des démo de cela, ou comment jouer de la musique avec des bananes :D

C'est pas mal du tout, on peut aussi mettre en evience des propriété de conductivité, et cela fonctionne très bien. Pour avoir sous la main un prof (à la retraite) qui joue avec ce type de démonstration avec des enfants de maternelle (pas avec Makey, mais avec des experiences de son cru), les enfants adorent ! Vous trouverez plus d'informations sur le site de [MakeyMakey](http://www.makeymakey.com).

### thymio

[https://www.thymio.org/fr:thymio](https://www.thymio.org/fr:thymio)

Possibilité de 4 à xx ans !!

### Minecraft

* 10+ ans
* programmation java et javascript
* utilisation IDE

Utilisation de scriptcraft (utilisation de moteur js)

[gnancraft.net](http://gnancraft.net/)


## Et le reste ???

Le reste fut passionnant aussi, je suis passé dans les conf :

* Archi découplé : RxJava
* Typescript - typer pour mieux coder
* Retours sur Java 8 par Jean Michel Doudou, une perle et un conférencier hors pair.
* Elasticsearch et Hibernate sont sur un bateau.
* Présentation automatisation let's encrypt
* Haskell - Bon là, j'en suis sur : je ne veux pas faire de Haskell :D
* Retour sur la transformation d'un dev en CTO au sein d'une startup tech
* CDI 2.0, les nouveautés.

## Les keynotes

Les keynotes des 2 derniers jours étaient articulées autours du thème de l'informatique et de la société. Nous avons pu entendre des orateurs comme Jean-Michel Billaut qui nous a parlé de la eSante, Richard Fontana qui a abordé le theme de la responsabilité et de l'éthique dans le developpement, nous en sommes tous responsable.

Parmis les autre orateurs, nous avons pu aussi entendre Natacha Quester-Séméon, qui nous a parlé de talents et de femmes. Qui sont sous representées dans notre métier, il faut bien l'avouer. Elle pousse l'initiative [#JamaisSansElles](https://twitter.com/search?q=%23JamaisSansElles&src=tyah), qui pousse à une plus grande representivité des femmes dans notre société, jusqu'a la place normale qui devrait être, a savoir à égalité. Avec un constat, souligné, non sans humour, que la solution devait venir de tous avec la phrase d'A.Einstein :

> La manière de penser qui a généré un problème ne pourra jamais le résoudre

Je ne pouvais que souscrire à cet axe.

Nous avons aussi vu Sébastien Broca qui nous a parlé de l'utopie et du travail ainsi que Matti Schneider qui nous presenté la startup d'état et la migration numérique que nous observons au sein des administrations. Il nous a fait un rapide état des lieux, ainsi que le focus sur ce qui se fait en France. Effectivement, nous leur devons des site comme [data.gouv.fr](https://www.data.gouv.fr/fr/), [adresse.data.gouv.fr](https://adresse.data.gouv.fr) et surtout la possibilité pour nous d'y participer. Je vous encourage à y jeter un coup d'oeil, et pourquoi pas vous jeter dans le bain de l'API publique :)

Et enfin, celle que j'ai préféré, et de loin par son analyse brillante de l'interaction (voir opposition) entre le monde de la politique et celui du code, fut celle de Fabrice Epelboin et sa présentation : ["Algorithmes, les nouveaux pouvoirs du développeur"](https://www.youtube.com/watch?v=h8UgB_-bXMc&index=9&list=PLTbQvx84FrAT-2zWY18x_mOtWf1kl22lW) qui met bien en perspective ces deux mondes. Mais aussi notre responsabilité dans le monde de demain et notre devoir dans le maintient de la confiance de tous dans le code et les données. *Si parmi toutes les vidéo des keynotes, vous ne deviez en voir qu'une, je vous conseillerai celle-là sans aucune hésitation*.

# En conclusion...

Alors, je voudrais profiter de cette modeste plateforme pour remercier toutes l'équipe de Devoxx France qui nous offre depuis 5 éditions une conférence extraordinaire et passionnante (et j'ai encore mes 5 pass accrochés au mur - c'est collectionneur un GeeK :D ). Ils sont responsable de mal de mes nuits blanches à chaque retour de conf. Je les remercie de l'energie qu'ils déploie pour cela soit toutes les années un plaisir renouvellé et un lieu d'échange privilégié.

Car oui, parlons en des échanges, et pas seulement pour recupérer des goodies (bien que mes filles en soit fan - j'ai bien du en faire rire un ou deux avec mes demande de t-Shirt taille mini, clever-cloud et JFrog sont leur préféré), mais aussi pour partager un point de vue, des experiences avec toute nos rencontres, ***c'est ça aussi, l'esprit Devoxx***.

Alors, oui, j'en serai l'année prochaine afin de reprendre une dose et me replonger avec delectation dans le dev, les rencontres et les decouvertes.

Encore merci à vous tous et rendez vous l'année prochaine.

![L'équipe Devoxx France](/images/post/devoxx/Devoxx-FR-06-1280x540.jpg)

Et je voulais aussi remercier [ApsideTOP](http://apside.fr), mon actuel employeur, de m'avoir permis ainsi qu'a un certains nombre de mes collègues d'avoir la chance de participer à Devoxx France.

Les liens :

* [La chaine youtube de Devoxx France](https://www.youtube.com/channel/UCsVPQfo5RZErDL41LoWvk0A) - des heures de vidéos, de quoi meubler vos nuits blanches.
* [Mon flipboard avec les slides disponible](https://flipboard.com/@ptitbob/devoxx-france-2016-i2gs466py).
