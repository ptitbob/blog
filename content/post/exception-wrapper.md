+++
title = "SpringMVC et les exceptions"
date = "2017-06-21T19:49:41+02:00"
description = "Comment traiter les réponses et les erreurs avec SpringMVC"
draft = true
tags = ["spring"]
words = ["spring","SpringMVC", "exception", "wrapper"]
metadescription = "Gestion des erreurs et traitement des exception dans le cadre d'une application SpringMVC exposant une API REST"
+++

## *Dis papa, comment on gère les erreurs avec SpringMVC ?*

La gestion des erreurs en java passe par la propagation d'exception.
Et le plus souvent l'interpretation et le traitement de celle-ci echoit à la dernière couche de l'application.

Le tout est d'éviter que la stacktrace apparraisse à l'écran, qui premièrement n'aide pas vraiment l'utilisateur, et en second ne fait quand même pas très serieux.

Heureusement la plupart du temps, nous faisons suffisament d'effort pour cela n'arrive pas et le traitement de l'exception est finalisé au niveau de l'exposition du end-point.
Et dans la plupart des applications une API REST renvoi en cas d'erreur un code HTTP correspondant à l'erreur et une structure normalisant les messages d'erreurs avec au cas ou un code propre à l'API ou au référentiel d'erreur de l'entreprise.

## Les exceptions et SpringMVC

SpringMVC permet l'exposition d'API REST et le plus souvent le traitement d'excpetion se passe comme cela :

{{< gist ptitbob 7f5fd3626ca68aaa7e2f59ed580ed8e7 >}}

Le code est valide et sans erreur, mais il ne repond pas au paradigme de la programmation par exception propre et de la bonne utilisation des génériques, mais surtout presente une mauvaise compréhension du framework utilisé.

Car même si il est testable de manière unitaire, comment généraliser une réponse à une problématique que peut se retrouver à plusieurs endroit dans le code.
Ici, nous avons la réponse a générer lorsque l'apple d'un update d'une ressource presente un path et un id (present dans les données de formulaire).

***Au final ce code presente ce qu'il ne faut pas faire dans le traitement des erreurs.***

### The good way !

Si on passe par la programmation par exception, ce qui est pour moi la meilleure mannière de traiter les erreurs en Java, voici ce que devrait donner le code precedent : 

{{< gist ptitbob 5963ad28883123a829632b9cae44b1d4 >}}

Le code est plus lisible.
La généricité est propre (`ResponseEntity<User>`).
Les exceptions sont un comportement attendu et maitrisé (`throws IncoherentResourceIdFormException, UpdateUserException`).
*Au final le code est plus testable !*

Maintenant que l'on laisse les exceptions buller, il va falloir les traiter (on les maitrise, hein ?!).
Pour cela SpringMVC propose deux possibilités de traitement (enfin 3, mais la 3eme découle de la 2nde - quelle teasing de malade !!!).

> Avant de passer aux explications, le code de cet exemple se trouve sur mon repo [github :: ptitbob/spring-exception-wrapper-example](https://github.com/ptitbob/spring-exception-wrapper-example).

### Exception et `@ResponseStatus`

La première solution, est de faire porter la réponse par l'exception elle-même. Voici un code levant une exception :

{{< gist ptitbob ff3a0c9252116430fe450df1ccb35ac7 >}}

Et le code l'exception : 

{{< gist ptitbob c216f02d5b55c5a3856a98e6d955237f >}}

quand on appele l'API, cela donne ça : 

```bash
[~]$ http :8080/ping
HTTP/1.1 418
Content-Type: application/json;charset=UTF-8
Date: Fri, 23 Jun 2017 21:18:29 GMT
Transfer-Encoding: chunked
{
    "error": "I'm a teapot",
    "exception": "org.shipstone.spring.ws.error.exception.PingPongException",
    "message": "Je ne suis pas une table de ping pong !!",
    "path": "/ping",
    "status": 418,
    "timestamp": 1498252709205
}
```

C'est simple, élégant et la structure renvoyée est "*verbose*".

***Cependant***, je vois au moins un problème potentiel.
La structure retournée est propre a Spring, vous la maitrisez pas.
*Et surtout*, la réponse porte la classe de l'exception, ce n'est pas forcement une bonne idée cette histoire. 
Une API REST doit être considérée comme une boite noire (à la rigueur grise) et exposer la classe de l'API n'est pas une bonne pratique (pour un dévevloppement de test ou un POC c'est génial).

### Les wrapper d'exception

Spring offre la possibilité de définir des wrapper d'exception (comme `JAX-RS`).

Le principe est de proposer un gestion de la réponse spécifique une exception particulière et ainsi de maitriser la réponse et de proposer une structure particulière pour porter l'erreur.
Le framework intercepte l'exception, et check dans son référentiel si il existe un traitement à celle-ci et si c'est le cas, lui transfert l'exception afin d'en attendre la réponse.
Cette solution offre l'avantage de permettre de gérer un référentiel d'erreur, de tester la réponse renvoyé de manière unitaire et en mode intégration (Test Spring avec les MockMvc).

Spring permet de définir des wrappers d'exception au sien de la classe exposant l'API REST ou de permettre de les excentrer dans une classe de wrapper d'exception, afin de les centraliser.
Nous allons les regarder dans cette ordre là.

#### Wrapper d'exception inclu à la classe

Reprenons le code de la mise à jour d'utilisateur.
Celi-ci lève deux exception : `IncoherentResourceIdFormException` et `UpdateUserException`.
La seconde est propre à la ressource "`User`", et nous allons la traiter dans la même classe.

Pour exposer un wrapper d'exception, il "suffit" d'annoter une méthode par `@ExceptionHandler(UpdateUserException.class)` (en lui passant la classe de l'exception a wrapper), et de passer en paramètre une instance de l'exception.

{{< gist ptitbob 618c4da834b29d1f0b414bc89645d468 >}}

La méthode renvoi une réponse typée (`ResponseEntity<ErrorMessage>`).
Le message est géré par l'exception, le code, par le wrapper, ce qui permet de gérer finement celui-ci.

L'appel à celle-ci renvoi ce type de résultat : 

```bash
[~]$ http -f PUT :8080/users/1 userId="1" login="toto" firstname="toto & titi"
HTTP/1.1 500
Connection: close
Content-Type: application/json;charset=UTF-8
Date: Sun, 25 Jun 2017 16:35:56 GMT
Transfer-Encoding: chunked
{
    "code": "600-01234",
    "message": "Erreur de mise à jour utilisateur : Mise à jour impossible - user : 1"
}
```

Cette solution permet aussi de tester de manière unitaire la réponse et de proposer aussi un fonctionnement pour les test d'intégration Spring.
(*Attention, mes méthodes pour cet exemple renvoi toujours des exceptions afin de souligner ce que je voulais montrer - une explication complète des test en fin d'article*)

###### Test unitaire

{{< gist ptitbob 5f05d2da22cdce64892f17020578f1ad >}}

###### Test intégration Spring

{{< gist ptitbob 376dd7f130e8b8e09defdeff6d122b89 >}}

***Vous avez maintenant un traitement de l'exception, testé, et cohérent avec vos données et votre API***. :)

#### Une classe pour les wrapper tous !

