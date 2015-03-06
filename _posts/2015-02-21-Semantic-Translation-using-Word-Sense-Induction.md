---

layout: post
title: "Semantic Translation using Word Sense Induction"
description: "Machine Translation enriched with the senses induced from the input source corpora"
category: [NLP]
tags: [NLP, Text Mining]

---
This post addresses the problem of automatic dictionary translation. In the
context of information retrieval the attempts in solving the multilinguality problems go back to the Salton's works in early 70-ties of the previous century. Since then, several methods of using multilingual dictionaries for improving information retrieval in multilingual text databases have been developed ([1],[2],[3]).

In [4] there was presented an approach for translating a lexical layer to a target language. The method, based on mining subject-similar repositories given in the source and target languages, was constructed in such a way that in the first step it discovered a seed dictionary, and then in the second step the seed was used for finding semantically similar terms. For this, it was called Seed Based Dictionary Builder (SBDB). However, the main drawback of the SBDB method was that the translation was term-to-term rather than meaning-to-term. As a result, the output dictionary had no references to the meanings of the translated terms. We enhance the method in such a way that instead of translating terms the method first discovers meanings of the terms, builds the context vectors for the meanings, and then translates the meanings. We denote the modified method as SBDB+. By assigning translations to the meanings rather than the terms we obtain much better precision of the phase of semantic translation.

The experiments have been performed for English (source) and Polish (target).
We used two text corpora, built from the English and Polish Wikipedia versions
respectively. The wikilinks between the articles, descriptive labels and interlin-
gual links have been neglected. So, the Wikipedia versions were used only as sets
of paragraphs.
The experiments were devoted to the question how multiple meanings of the terms may deteriorate the quality of translations, and how discovering meanings and assigning them to the terms can improve the quality. We have selected a sample of polysemic terms from Wikipedia, for which the number of meanings was at least 5 and performed the following two
tests:
 
 - for the sample of highly polysemic terms we have build the vector space,
and then tested the quality of translation without taking into account the
meanings identified by the SnS algorithm;
 - for the same sample we have identified the meanings and then built the
vector space for the pairs (term, meaning)

![Comparison of the translation quality for the polysemic terms]({{ site.url }}/assets/images/smt-wsi.jpg)

The above figure illustrates the results of the experiments. As one can see the SnS
phase of finding the meanings and then building the context vectors for the pairs (term;meaning) improves the quality of translations essentially. In the experiments the quality of translations can be seen in the function of the context size parameter.
We also noticed that for the vector space without meanings the optimal value for the context size is much higher than for the vector space with meanings (about 1000 versus 100).

##### References

[1]: Hull, D.A., Grefenstette, G.: Querying across languages: a dictionary-based ap-
proach to multilingual information retrieval. In: Proceedings of the 19th annual
international ACM SIGIR conference on Research and development in information
retrieval, ACM (1996) 49-57.

[2]: Ballesteros, L., Croft, W.: Phrasal translation and query expansion techniques for
cross-language information retrieval. In: ACM SIGIR Forum. Volume 31., ACM
(1997) 84-91.

[3]: Pirkola, A.: The effects of query structure and dictionary setups in dictionary-
based cross-language information retrieval. In: Proceedings of the 21st annual
international ACM SIGIR conference on Research and development in information
retrieval, ACM (1998) 55-63.

[4]: Krajewski, R., Rybinski, H., Kozlowski, M.: A seed based method for dictionary
translation. In: Foundations of Intelligent Systems, LNCS, Springer. Volume 8502.
(2014) 415-424.