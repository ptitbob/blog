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

## Les excpetions et SpringMVC

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
http :8080/ping
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
*Et surtout*, la réponse porte la classe de l'exception, ce n'est pas forcement restful cette histoire. Une API REST doit être considéré comme une boite noire (à la rigueur grise) et exposer la classe de l'API n'est pas une bonne pratique (pour un dev de test ou un POC c'est génial).

