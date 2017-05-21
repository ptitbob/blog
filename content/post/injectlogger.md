+++
date = "2017-05-21T01:05:38+02:00"
description = "Injection du logger avec le framework Spring"
draft = true
tags = ["spring"]
title = "Injection du logger"
words = ["spring","injection", "logger"]
metadescription = "Injection du logger avec le framework Spring"
+++

Voici un petit exemple permettant d'injecter un logger directement au niveau d'un controller, d'un composant via une annotation avec le framework **[Spring](https://spring.io/)**.

Le but est d'obtenir quelque que chose comme cela :

{{< gist ptitbob c800a21bebb8aff94da29c92a6f10c86 >}}

Le fonctionnement est simple, l'annoation va permettre à Spring d'injecter un logger identifié à la classe (nom complet) si aucune informations n'est ajoutée.
Par contre, si un nom est spécifié, le logger sera instancié avec ce nom (log).
Cela rend le code plus lisible que cela : 

{{< gist ptitbob 5bd80d052c4f65890b0f765a5dfdcb82 >}}

Et de permettre une gestion des logger plus fluide. Mais il est vrai que c'est ténu comme raisonnement.

### Et sous le capot, cela se passe comment ?

Tout commence par l'annotation, simple et classique.

{{< gist ptitbob 8208e2519a721763ec4dda916b80f962 >}}

Notez que l'annotation comporte un champs qui permettra à l'occasion de nommé le fux de log auquel sera rattaché le logger.
La valeur par défaut est définie pour que le logger ai un comportement par défaut lors de l'injection.

Maintenant, il faut gérer l'injection du logger en utilisant les processus du framework Spring.
J'implemente donc l'interface spring `BeanPostProcessor` dans une classe annoté `@Component`. 
*L'annotation `@Component` permet la prise en compte de celle-ci par Spring, et l'interface est elle aussi prise en compte comme processus d'initialisation des bean de notre projet.*

L'interface `BeanPostProcesso` expose un contrat avec deux méthode à implémenter : 

* **`postProcessBeforeInitialization`** : Avant l'initialisation du/des bean
* **`postProcessAfterInitialization`** : Après l'initialisation du/des bean

Dans le cadre de l'instaciation d'un champs des bean du projet, c'est la méthode `postProcessBeforeInitialization` qui va être le centre de ce post. :)

### Injectons le logger.

{{< gist ptitbob 253fb8dbfe819f347c227175a9142f56 >}}

La méthode reçoit en paramètre le bean analysé par Spring ainsi que son nom (pour nous, peu d'importance ici).

Je parcours tous les champs du bean, et si je trouve l'annotation `@MyLogger`, je passe à l'injection du logger.
Je rends le champs accessible afin de pouvoir l'affecter.
Puis selon que le nom ai été défini ou pas, j'injecte un logger basé sur le nom de la classe (pattern classique de création de logger), soit j'injecte un logger avec le nom fourni.

*Vous remarquerez que je ne rebascule pas la visibilité du champs à l'initiale, a vrai dire, on s'en fout un peu car tout se passe à l'éxécution.*

### Exemple...

***Et cela pourrai servir à quoi ?***

On peut imaginer pléthore de possibilité de pouvoir injecter plusieurs logger dans une classe.
Par exemple logger les accès par IP/Utilisateur de mannière simple.
Et de pouvoir les exploiter via un outil comme [Kibana](https://www.elastic.co/fr/products/kibana)...
Bon, d'accord cela fait un peu big brother, mais c'est mon exemple ;).

Prenons une méthode d'un `@RestController` :

{{< gist ptitbob 9eadea4d196850127fe6b7ba826e7ad5 >}}

La sortie de log lors de l'appel de la méthode, donnera cela : 

```
2017-05-20 23:49:06.905  INFO 362 --- [main] org.shistone.logger.ws.rs.HelloEndpoint  : paramètre name : cher visiteur
2017-05-20 23:49:06.905  INFO 362 --- [main] access                                   : localhost[null]
```

Les sources de cette exemple se trouve ici : [github.com/ptitbob/injectlogger](https://github.com/ptitbob/injectlogger)

