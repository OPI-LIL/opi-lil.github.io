---
layout: post
title: "Lexical similarity between Polish Language and other European ones"
description: "Lexical similarity based on the Damerau-Levenshtein edit distance measure and BabelNet lexicon"
category: [NLP]
tags: [NLP, Text Mining]
---


The idea of examining the usefulness of lexical similarity in translation is based on the observation that some words in both the source and target language are similar in both form and meaning. Such kind of parallels between languages are subject of intensive research by the linguists. McMahon [1] discusses how languages belonging to the same family diverge, whereas Kranich et al [2] give an interesting account of how languages belonging to different families influence each other. The natural conclusion is that these similarities can be helpful in building multilingual dictionaries, not only within a language family. 

<!--more--> 

The causes of lexical similarity between different languages are manifold. For languages belonging to the same group, the similarities are to a large extent attributable to their common past. For both related and unrelated languages, borrowing may be an important source, due to factors such as technology transfer, cultural influence, language contact etc. This is the case of e.g. German (Germanic) and Polish (Slavic) or French (Romance) and English (Germanic).

In linguistics, words that share origin are known as cognates, and while part of them are false friends, a great portion preserves roughly the same meaning across languages. The difficulty lies in the fact that their graphical forms are necessarily as heterogenous as the languages they belong to. On the other hand, some words display striking similarities in form and meaning without being cognates. These may be an impediment for etymologists, but are still helpful for translators. We set out to determine how many Polish words have "true-friend" cognates or false cognates in other European languages.

One set of tools for measuring similarity between character strings, e.g. written words, are edit distances. We used the Damerau-Levenshtein distance. This type of string metric was introduced in a 1965 paper by Levenshtein. It defines the distance between two strings as the minimal number of edits required to convert one string into the other. In the simpler Levenshtein distance, the only edit operations allowed are deletion of a single character, insertion of a single character, and substitution of a single character. In Damerau's version, transposition of a single character is allowed as well. 

The source of corpora was [BabelNet](http://babelnet.org) "both a multilingual encyclopedic dictionary, with lexicographic and encyclopedic coverage of terms, and a semantic network which connects concepts and named entities in a very large network of semantic relations, made up of more than 13 million entries . . . . BabelNet 3.0 covers 271 and is obtained from the automatic integration of" Wikipedia, Wiktionary, Wikidata, OmegaWiki, WordNet, and Open Multilingual WordNet. We iterated the lexicon of terms using BabelLexiconIterator. For each Polish word we found translations in the following European languages: Bulgarian, Croatian, Czech, English, French, German, Greek, Hungarian, Italian, Latvian, Lithuanian, Norwegian, Romanian, Russian, Serbian, Slovak, Slovene, Spanish, Swedish, Turkish, Ukrainian (21 in total). We paired each translation with its Polish equivalent. Then, the Damerau-Levenshtein distance was calculated for all pairs. If the result was below 4 edits, the foreign equivalent was counted as a _similar word_. The summary of this process is presented in the table.

The most interesting insights are:
 1. For most language families, the language that is lexically closest to Polish (in bold) has 10--16% _similar words_ (distance from 1 to 3)
 2. About 35--50% of the matches are mirror translations (distance 0)
 2. Distance 0 words are mainly proper nouns, left untranslated, e.g. _Tina Thompson_, _Gąbin_ (town)
 3. Distance 1 -- the most similar language in the range of 1 edit is German
 4. Distance 2 -- the most similar language in the range of 2 edits is French
 5. Distance 3 -- the most similar language in the range of 3 edit is French
 6. Surprisingly poor results for Bulgarian, Russian and Ukrainian, all of them Slavic languages, is caused by the incompatibility of alphabets. Their Cyrillic alphabets are most commonly Latinized using conventions originating in English-speaking countries, e.g. _sh, zh, ch_ for _ш, ж, ч_, that are quite different from the spelling of related sounds in Polish (here, _sz, ż, cz_.)

| Language pairs | Number of Translations | Distance 0 | Distance 1 | Distance 2 | Distance 3 |
|:-----------|------------:|:------------:|:------------:|:------------:|:------------:|
|*Slavic*|------------|------------|------------|------------|------------|
|**PL->SK**| **351131** |  **152075** |  **22273** |  **17623** | **15195**
|PL->CS| 358580 |  140790 |  21487 |  18227 | 16773
|PL->HR| 317785 |  114257 |  17435 |  16246 | 12887
|PL->SL| 298099 |  116485 |  15747 |  14642 | 11678
|PL->BG| 335071 |  70285 |  2407 |  3004 | 3906
|PL->RU| 478340 |  69435 |  1852 |  2230 | 2803
|PL->UK| 395224 |  59894 |  1243 |  1562 | 3009
|PL->SR| 325521 |  2849 |  924 |  1133 | 2300
|*Baltic*|------------|------------|------------|------------|------------|
|**PL->LT**| **326092** |  **101949** |  **14306** |  **20115** | **19446**
|PL->LV| 300814 |  95690 |  12746 |  15851 | 17397
|*Germanic*|-----------|------------|------------|------------|------------|
|__PL->EN__| __704147__ |  __372762__ |  __20514__ |  __17633__ | __17024__
|PL->DE| 531315 |  244947 |  25738 |  19273 | 16938
|PL->SV| 441750 |  196176 |  18417 |  15405 | 13856
|PL->NO| 387074 |  162848 |  15673 |  14704 | 13341
|Romance|------------|------------|------------|------------|------------|
|**PL->FR**| **549762** |  **250293** |  **22376** |  **23105** | **19956**
|PL->IT| 525503 |  247530 |  21607 |  19256 | 19944
|PL->ES| 486417 |  209889 |  21270 |  19043 | 18476
|PL->RO| 373520 |  163404 |  19187 |  14439 | 13362
|Hellenic|------------|------------|------------|------------|------------|
|__PL->GR__| __312738__ |  __63362__ |  __5130__ |  __3704__ | __4586__
|Non-Indo-European|------------|------------|------------|------------|------------|
|__PL->HU__| __351664__ |  __129552__ |  __12654__ |  __11520__ | __10970__
|PL->TR| 323349 |  119628 |  12643 |  10836 | 9731



###References
[1]: McMahon A. 1994. Understanding language change. Cambridge University Press.

[2]: Kranich, Svenja, Viktor Becher, and Steffen Höder. 2011. A tentative typology of translation-induced language change. In: Multilingual Discourse Production: Diachronic and Synchronic. 
