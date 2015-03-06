---

layout: post
title: "Exploration of induced senses by SnS"
description: "Illustration of the output of the SnS - Word Sense Induction algorithm"
category: [NLP]
tags: [NLP, Text Mining]

---

In the previous post (SnS - Word Sense Induction method), we have proposed the method called SenseSearcher (SnS). It is a word sense induction algorithm based on closed frequent sets and multi-level sense representation. SnS is a knowledge-poor approach, which means it does not need any kind of structured knowledge base about senses as well as the algorithms that have embedded deep language knowledge.

<!--more-->

This post presents senses discovered by SnS from five hundred Wikipedia paragraphs for terms T={*apple, bass, plant, mouse, virus, bear, block, bow, cat, jaguar, nadirg*}. The figures show print screens of SnS tool identifying representative senses for the terms from T.

Figure 1 presents senses for the term **apple** S = {*computer, music, country, fruit,
golden apple, film, book*}, where *computer* refers to Apple Inc. products, *music* to albums and bands, *country* to geographical names, *fruit* to a fruit of a tree, *golden apple* to a story element in ancient mythologies and *film, book* are related to movies and literature titled with word apple.

![Senses for apple extracted from Wikipedia paragraphs]({{ site.url }}/assets/images/apple.jpg)

Figure 2 presents senses for the term **bass** S = {*born, guitar, album, fish*}, where *born*
refers to famous people with surname *Bass*, *guitar* and *album* are related to music, and *fish* refers to various freshwater and saltwater species.
![Senses for bass extracted from Wikipedia paragraphs]({{ site.url }}/assets/images/bass.jpg)

Senses for the term **mouse** S = {*species, cartoon, computer, child, band*} are shown in
figure 3. Sense *species* refers to animal, *cartoon* to Tom&Jerry animated cartoon films,
*computer* is related to a pointing device, *child* to a literature dedicated to children, and *band* to the rock band, which name contains word mouse.
![Senses for mouse extracted from Wikipedia paragraphs]({{ site.url }}/assets/images/mouse.jpg)

In figure 4 senses of the term **jaguar** are presented. Namely, there are senses S =
{*car, game, film, city, species*}, where *car* refers to brand of cars, *game* to a video game console, *film* refers to movies, *city* concerns cities related with Maya cultivation of jaguar.

![Senses for jaguar extracted from Wikipedia paragraphs]({{ site.url }}/assets/images/jaguar.jpg)

Figure 5 presents senses S for the term **nadir**. Mainly, they are: *shah* (referring to
Persian shah called Nadir Shah), *point* (the direction pointing), or *banu* (jewish tribe called banu nadir).

![Senses for nadir extracted from Wikipedia paragraphs]({{ site.url }}/assets/images/nadir.jpg)