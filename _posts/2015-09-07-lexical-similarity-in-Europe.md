---
layout: post
title: "Lexical similarity between Polish Language and other European ones"
description: "Lexical similarity based on the Damerau-Levenshtein edit distance measure and BabelNet lexicon"
category: [NLP]
tags: [NLP, Text Mining]
---


The idea of using lexical similarity is based on the assumption that some words exist in a similar form in the source and target languages. Actually, the similarities between languages are subject of intensive research by the linguists. McMahon [1] discusses how the languages belonging to the same family diverge, whereas Kranich et al [2] give an interesting example how the languages belonging to different families influence each other. It seems therefore to be straightforward to apply the similarities for building dictionaries not only for the languages belonging to the same family.

<!--more--> 

Linguists indicate various reasons of similarities between the dictionaries of the different languages. For the languages belonging to the same group, to large extent the common roots in the past decide on the similarities. Additionally, an influence of one language to another one may result from various factors, such as, e.g. technology transfer, cultural influence, etc. This may also refer to the languages belonging to different groups, like e.g. German and Polish.

The words having similar form (and the same meaning) are named by linguists cognates, and it turns out that they appear quite often across the modern languages. The problem with cognates is that they are in slightly different forms (due to the linguistic rules in the source and target languages).  We decided to verify how many cognates exist between the Polish and other European languages.

To measure similarity between words an edit distance computation algorithm can be employed. We used a Damerau-Levenshtein edit distance measure. The simplest form of edit distances were introduced in the by Levenshtein (1965). The distance between two strings is defined as the minimal number of edits required to convert one into the other. In the simpler Levenshtein distance, the allowable edit operations are the deletion of a single character, the insertion of a single character and the substitution of one character for another. In Damerau's version, transposition is also an allowable edit. 

As a corpora we have used the [BabelNet](http://babelnet.org). BabelNet is both a multilingual encyclopedic dictionary, with lexicographic and encyclopedic coverage of terms, and a semantic network which connects concepts and named entities in a very large network of semantic relations, made up of more than 13 million entries, called Babel synsets. BabelNet 3.0 covers 271 and is obtained from the automatic integration of Wikipedia, Wiktionary, Wikidata, OmegaWiki and Wordnet. We have iterated through the lexicon of terms using BabelLexiconIterator. For each Polish word we have verified translations in the below mentioned 21 European languages. Existing translations were lexically compared using Damerau-Levenshtein edit distance measure, those ones below 4 edits were counted as similar words. The result of this process is summarized in the table.
The most interesting insights are:

 1. In each group of language the most similar lexically ones with Polish (in bold) have from 10-16% similar wordings (distance from 1 to 3)
 2. About 35-50% of translations are the mirror ones (distance 0)
 2. Distance 0 words are mainly proper name, locations, person names, which have not been translated e.g., Tina Thompson, town Gąbin etc
 3. Distance 1 - the most similar language in the range of 1 edit is German
 4. Distance 2 - the most similar language in the range of 2 edits is French
 5. Distance 3 - the most similar language in the range of 3 edit is French
 6. Very poor results for the Bulgarian, Russian, Ukrainian languages is caused by the different alphabet (Polish language is the Latin based, and the others are Cyrillic based ones)

| Language pairs | Number of Translations | Distance 0 | Distance 1 | Distance 2 | Distance 3 |
|:-----------|------------:|:------------:|:------------:|:------------:|:------------:|
|West Europe|-----------|------------|------------|------------|------------|
|**PL->FR**| **549762** |  **250293** |  **22376** |  **23105** | **19956**
|PL->DE| 531315 |  244947 |  25738 |  19273 | 16938
|PL->IT| 525503 |  247530 |  21607 |  19256 | 19944
|PL->ES| 486417 |  209889 |  21270 |  19043 | 18476
|PL->EN| 704147 |  372762 |  20514 |  17633 | 17024    
|Nord Europe|------------|------------|------------|------------|------------|
| **PL->SV**| **441750** |  **196176** |  **18417** |  **15405** | **13856**
|PL->NO| 387074 |  162848 |  15673 |  14704 | 13341
|Central Europe|------------|------------|------------|------------|------------|
|**PL->CS**| **358580** |  **140790** |  **21487** |  **18227** | **16773**
|PL->SK| 351131 |  152075 |  22273 |  17623 | 15195
|PL->RO| 373520 |  163404 |  19187 |  14439 | 13362
| PL->HU| 351664 |  129552 |  12654 |  11520 | 10970
|South Europe|------------|------------|------------|------------|------------|
|**PL->HR**| **317785** |  **114257** |  **17435** |  **16246** | **12887**
|PL->SL| 298099 |  116485 |  15747 |  14642 | 11678
|PL->TR| 323349 |  119628 |  12643 |  10836 | 9731
|PL->HE| 312738 |  63362 |  5130 |  3704 | 4586
|PL->BG| 335071 |  70285 |  2407 |  3004 | 3906
|PL->SR| 325521 |  2849 |  924 |  1133 | 2300
|East Europe|------------|------------|------------|------------|------------|
|**PL->LT**| **326092** |  **101949** |  **14306** |  **20115** | **19446**
|PL->LV| 300814 |  95690 |  12746 |  15851 | 17397
|PL->RU| 478340 |  69435 |  1852 |  2230 | 2803
|PL->UK| 395224 |  59894 |  1243 |  1562 | 3009



###References
[1]: McMahon A. 1994. Understanding language change. Cambridge University Press.

[2]: Kranich, Svenja, Viktor Becher, and Steffen Höder. 2011. A tentative typology of translation-induced language change. In: Multilingual Discourse Production: Diachronic and Synchronic. 
