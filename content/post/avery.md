+++
date = "2016-05-15T00:53:52+02:00"
description = "what's up doc"
metadesctiption = "Reproduire le logo Tex Avery en CSS C'est fun"
title = "Tex Avery style"
tags = [ "css", "fun"]
+++

J'ai toujours été un grand fan des dessins animés de Tex avery... Et unjour j'ai eu envie de recréer et de détourner le fameux logo de fin.

Si si, vous voyez lequel :

![that's all folk](/images/post/avery/thatsallfolk.jpg)

## Tex Avery avec style

Mais la reproduire au format d'une image n'est marrant, et surtout une image c'est lourd ! Donc le but serai de créer la même avec le minimum de ligne de code et sans JS. Bon, vu mon niveau en css, c'était pas gagné...

## Challenge accepted

Donc, on va faire simple, voici le code de la page html :

```html
<!doctype html>
<html lang="fr">
<head>
  <meta charset="utf-8">
  <title>that's all folk</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
</body>
</html>
```

Difficile de faire plus simple (encore que je pourrai supprimer la balise de classe text)... Tout se passe au niveau du style.

> Reproduire une image en css, t'es pas un peu malade man ?!! (dixit ma grand mère)

Mais non, quand on y regarde bien, l'image est composé de très peu de motif, dont qui est répétitif.

Donc nous allons reproduire tout d'abord le fond et le placer

```css
overflow: hidden;
color: #FFFFFd;
width: 100%;
height: 99vh;
background-position: center;
```

```overflow: hidden``` permet de supprimer les scroller (du moins du chrome), en réalité on demande au navigateur de cacher la contenu en trop (ce qui revient au même). Puis nous demandons à utiliser toute la largeur et 99% de la vue relative au viewport (```height: 99vh```), c'est l'astuce qui permet de positionner le fond. Et nous positionnons le fond.

Maintenant, attaquons nous au motif en cercle :

```css
background:
  url("images/Mammoth_Seated_256x256.png")  no-repeat 50.5% 290px ,
  radial-gradient(
    circle at 50% 400px,
    #000e1b 140px,
    rgba(0,0,0,0) 10px
  ),
  repeating-radial-gradient(
    circle at 50% 400px,
    #b9561d,
    #a72211 18px,
    #98180f 25px,
    #590004 70px
  );
```

Nous utilisons une particularité de CSS3, a savoir les background multiple. La première étape, est l'inclusion de mon logo (ou du votre). Puis nous mettons en place le cercle noir. Puis nous utilisons le dessin de cercle avec dégradé repétitif (```repeating-radial-gradient```) et c'est là que réside l'astuce. ***Retenez bien que lors de l'interpretation de fond multiple, les navigateurs procede toujours en partant du dernier***. Le resultat est visible [là](/lab/avery/step1.html).

Pas mal, non ? :)

> Et le texte bonhomme ?

J'y viens...

## That's all Folks

Le but est de trouver la bonne police, facile a utiliser. Pour cela j'utilise une [google font](https://www.google.com/fonts) afin de déléguer le stockage à google et l'incluant directement dans la feuille de style (*pensez simplement à placer l'import en tête de fichier*).

```css
@import url(http://fonts.googleapis.com/css?family=Dancing+Script);
```

Pour le placement du texte, j'avais plusieurs solutions. La gestion des pseudo balise css (ce qui serai le plus simple a mon avis) ou la mise en place d'une balise HTML bidon (ce qui est le plus facile). Vu mon niveau en CSS, j'ai choisi la solution de facilité en plaçant une balise vide dans le corp de la page. ce qui donne le code suivant :

```html
<!doctype html>
<html lang="fr">
<head>
  <meta charset="utf-8">
  <title>that's all folk</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div class="text"/>
</body>
</html>
```

Et en definissant le style en deux étape :

```css
div.text:before{
  content: "That's all Folks !";
}
```

En utilisant la pseudo balise ```:before``` je me permets d'injecter mon texte dans la balise. Reste plus qu'a définir la mise en forme du texte en jouant avec les ombres portées (```text-shadow```).

```css
div.text {
  z-index:10;
  width: 800px;
  height: 180px;
  margin: auto;
  padding-top: 100px;
  font-family: 'Dancing Script', cursive;
  font-size: 110px;
  text-align: center;
  text-shadow: 2px 0 0 #590004, -2px 0 0 #590004, 0px 2px 0 #590004, 8px 8px 10px black, 13px 13px 12px black, 20px 20px 15px black;
```

J'ai positionné l'index de position z à 10 pour éviter toute interaction avec le contenu. Et voilà, [le tour est joué](/lab/avery/) !!!

Il y aurait encore a faire, car il faudrait courber le texte pour coller à l'image originale, si vous avez envie de jouer, allez y, dites moi (via un petit commentaire) Et comment vous y etes parvenu ;) :)

***Have fun !!***
