---
layout: post
title: "Color translation using BabelNet and ML approaches"
description: "This post refers to the topic of automatic machine translation of colors"
category: [NLP]
tags: [NLP, Machine Translation]
---

This post refers to the topic of automatic machine translation of colors, but not the simple dictionary ones (as *green, yellow or blue*) but the compound, novel descriptors (like *olympic gold*, *fatboy black*, *tyson orange* etc). Naive approach based on dictionary of colors is no able to cover more than few percentages of cases. We were forced to use more sophisticated methods, what is described below.

<!--more-->

Machine translation is the is a sub-field of computational linguistics that investigates the use of software to translate text from one language to another. Most of works is dedicated to the SMT methods i.e. statistical methods, which aim is to find regularities between aligned words. This post is about domain lexicon translation, namely translation of original colors describing e-commerce offers. 

Cooperation with one e-commerce company give us about 150 000 item offers described by the attributes:

- oi_id - original item id
- level_1_ebay - main category (level 1)
- level_2_ebay - subcategory (level 2)
- ea_id - seller id
- original_title - title of the item (not translated)
- translated_title - title of the item (translated)
- src_site - source site (like DE)
- dst_site - destination site (like EN)
- src_color - source color
- trg_color - translated color

The below sample offer's records are presented using csv notation (the order of attributes is as defined above):

>     1xxx,888,7294,yyyy,NEW DEDA PADDED ROAD BIKE CYCLE HANDLEBAR BAR TAPE ALL COLOURS,Bande Adhesive Guidon Velo Deda Mistral Cyclisme 8 Couleurs Neuf,UK,FR,olympic gold,Doré Olympique
>  
>     1xxx,888,7294,yyyy,NEW DEDA PADDED ROAD BIKE CYCLE HANDLEBAR BAR TAPE ALL COLOURS,Bande Adhesive Guidon Velo Deda Mistral Cyclisme 8 Couleurs Neuf,UK,FR,gun grey,Gris Acier

Ours goal was to conduct research on the color translation method, which can improve the process, reducing the humans effort in this process.

Maybe some of You read my previous article about dictionary translation using SBDB+ (Enhanced Seed based Dictionary Builder). There was presented an approach for translating a lexical layer to a target language. The method, based on mining subject-similar repositories given in the source and target languages, was constructed in such a way that in the first step it discovered a seed dictionary, and then in the second step the seed was used for finding semantically similar terms. Instead of translating terms the method first discovers meanings of the terms, builds the context vectors for the meanings, and then translates the meanings. This method cannot be used because of few reasons: lack of two domain-oriented but independent repositories that are huge enough (we have only one repository with short phrases what brings to short contexts, and irrelevant representation), colors are difficult to be discriminated by contexts (the same product might be *yellow*, *green* or *metallic black*).

Using the reference data set I have conducted several initial computations in order to verify/estimate some figures, what can be achieved using a naive approach. The naive approach means using simple machine learning methods and exploiting some external broad knowledge base with well defined translations between many languages (i.e. [BabelNet](http://babelnet.org/) is both a multilingual encyclopedic dictionary, with lexicographic and encyclopedic coverage of terms, and a semantic network which connects concepts; it covers now 200 languages, and in its current local copy weights more than 50 GB). 

**Number of all instances to translate:** ~146 000
**Number of mirror translations:** ~30 000 (the color translation and the source color are equal)
**Number of complete translations:** ~3 000 (translations exist explicite in the BabelNet)
**Number of partial-unorder translations:** ~26 000 (translations of compound colors, which are reached by translating separated, source words without taking into account the order of words in the target phrase, i.e. we translate in the bag-of-words manner, the order is neglected)

Above analysis give me possibility to define the type of  translations, which can be reached using computational methods: 

1.  Partial UnOrder Translations (each compound word is divided into set of uni-grams, which are translated independently, and we check whether the bag-of-words are equal)
2.  Complete Translation (each single word has assigned proper translation in the BabelNet Lexicon)
3.  Mirror Translation (for each language pairs there is built a classifier in order to identify whether this offer is likely to have the mirror color translation) 

Iterating through the offers the proposed translation method works in three ordered steps: 

 1. Identify Mirror translation using kNN classifiers
 2. Otherwise try to perform a complete translation (if the source word has some translation in the BabelNet, this translation is retrieved)
 3. Otherwise try to perform a partial-unorder translation (colour compound words are rarely placed in the BabelNet, therefore I tokenized them into uni-grams, and in the next step I translated them independently)




## Complete translation

The color in the given source language is found in the BabelNet dictionary, and it has assigned translation for the given target language.

####Examples of complete translations

ultraviolett->ultravioletto || DE->IT
Complete translation

Vicuna->vigogne || EN->FR
Complete translation

Brown->marrone || EN->IT
Complete translation

Timbuktu->tombouctou || DE->FR
Complete translation

Florenz->firenze || DE->IT
Complete translation

Purple->pourpre || EN->FR
Complete translation

Sulphur->azufre || EN->ES
Complete translation

## Partial translation

The color in the given source language is not found in the BabelNet dictionary, but the uni-grams creating this color can be translated, or they are copied one-to-one (if there is no translation in the BabelNet, or the uni-gram is a word from another language than the given source language like *grape* in German). Assumption: the order of words is neglected i.e. if the proper translation is 'verde muschio' and ours approach returns 'muschio verde' we count it as a positive translation.


####Examples of of proper partial unorder translations

Fatboy Black->fatboy negro || EN->ES
Partial matching
Partial translation: negro
Verified POSITIVELY using partial matching

Tyson Orange->tyson arancione || EN->IT
Partial matching
Partial translation: arancione
Verified POSITIVELY using partial matching

Moss Green->verde muschio || EN->IT
Partial matching
Partial translation: muschio
Partial translation: verde
Verified POSITIVELY using partial matching

10 Aubergine Lila Grape->10 eggplant purple grape || DE->EN
Partial matching
Partial translation: eggplant
Partial translation: purple
Contained explictily: Grape
Verified POSITIVELY using partial matching


## Mirror translations

The first step is a  statistical analysis of the mirror and non-mirror translations as the attempt to draw some probabilities about the state: mirror/non-mirror per a language pair.

After grouping and sorting NonMirrors (110k), for each language pair there is a given number of instances, and the normalized cardinality (NonMirrors are about 3.5x more than Mirrors, therefore assuming uniform distribution we normalized the supports in order to compute the probability of being the mirror translation):

> EN-DE: 19539 (normalized: 5500) 
> EN-ES: 18470 (normalized: 5200) 
> EN-FR: 16035 (normalized: 4571) 
> EN-IT: 15296 (normalized: 4300) 
> DE-FR: 14847 (normalized: 4200) 
> DE-EN: 12513 (normalized: 3500) 
> DE-IT: 8479 (normalized: 2428)  
> DE-ES: 7604  (normalized: 2128) 
> DE-CA: 346 
> EN-EN: 226 


After grouping and sorting Mirrors (32k), for each language pair there is a given number of instances, and probability of being the mirror translation based on the source-target language pair:

> DE-EN: 7880 - 60% (likelihood of being the mirror one based on one feature - source-target language pair) 
> EN-FR: 5198 - 50% 
> EN-DE: 4825 -47% 
> EN-IT: 4699 - 52% 
> EN-ES: 2884 - 35% 
> DE-FR: 2117 - 33% 
> DE-IT: 2022 - 45% 
> DE-ES: 1773 - 46% 
> EN-EN: 1022 - (EN-EN appears because ireland, australia, us languages was replaced with the en language) 
> DE-CA: 437
> EN-CA: 58 
> CA-FR: 1


 
Second step was aimed at building the knn (k=5) classifiers per a language pair, and evaluating them in the balance mode (70% training -30% testing set).

I created the test beds based on the balanced distribution among classes i.e. for the pair DE-EN I have 7880 instances tagged as mirrors, and  12513 as non-mirrors, I shuffled them in order to inject some randomness, and I create the training set (5515 mirror instances and 5515 non-mirror ones),  and testing set (2364 mirror instances and 2364 non-mirror instances). The training and testing sets do not have any intersections and are balanced according to the class distribution, what is very important otherwise we have biased model (over-fitting). 

Knn-classifier exploits only one features - the source title, the language is not used as a feature ( we built each classifier per the given language pair in order to retain the representativeness, which is variable between language pairs).

We built classifiers for each language pair, some brief reports from the evaluation (average accuracy reported on the testing set):

> DE-EN: 89% EN-FR: 85% EN-IT: 85%

Those results are promising, because for some pairs of language we create models with almost 90% accuracy in identifying mirrors.

##Summarizing
 
Complete translations are only the minor fraction of all  instances (average dictionary of colors, or even the extended dictionary of colors covers only little amount of e-commerce cases, e.g. it's difficult to identify such phrases as a color: *3 Creme zartes Gelb, 2 Sand Strand Dimples, Nude Beige Cream Patent Buckles Strappy*). Nevertheless, there is a chance to get more from the partial-unorder approach, where translation is performed per uni-gram, but the question is how the order of words is crucial in this case (if we need to fit the Language Model in 100% it means that after some partial translation there must be processed ordering step typical for each language according to some PoS patterns).

Last output from my survey is focused on mirror color translations (instancese where the source colour and target colour are equal, we found that 32k/140k translations are mirrors). I have built the set of knn classifiers for each pair of languages, and I have evaluated them using balanced tranining/testing set (70-30%). The results are promising, the average accuracy for some pairs of languages are near 90%. 
