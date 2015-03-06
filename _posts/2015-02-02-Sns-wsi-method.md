---

layout: post
title: "SnS - the Word Sense Induction method"
description: "The description of novel Word Sense Induction Method called SnS"
category: [NLP]
tags: [NLP, Text Mining]

---

In the paper [1] there was proposed the method called SenseSearcher (SnS). It is a word sense induction algorithm based on closed frequent sets and multi-level sense representation. SnS is a knowledge-poor approach, which means it does not need any kind of structured knowledge base about senses as well as the algorithms that have embedded deep language knowledge. Senses induced by SnS characterize better readability (are more intuitive), mainly because SnS
discovers a hierarchy of senses showing important relationships between them. In other words the proposed method creates structure of senses, where coarse-grained senses contain related sub-senses (fine-grained senses), rather than  at list of concepts.

The SnS algorithm consists of five phases (one prephase and four proper phases), which we present below. The pseudocode of the whole algorithm is presented in [1]. Prephase is performed once per corpora and is devoted to building an inverted index for the corpus. Usually we use unstructured raw corpus split into paragraphs. Each paragraph is seen as a "document" persisted in the index.

In Phase I, with a given term we run a query on the index, and find paragraphs related to the term. Then we convert them into context representations (bag of words). The procedure of building contexts contains tokenization, PoS tagging, named entity detection.

In Phase II contextual patterns are discovered from contexts generated in Phase I. The patterns are closed frequent termsets in the context space. The contexts are treated as transactions (itemsets are replaced by termsets) and the process of mining closed frequent termsets is performed with the use of the CHARM algorithm [2].

Phase III is devoted to forming contextual patterns into sense frames, building a hierarchical structure of senses. In some exceptional states few sense frames may refer to one sense, it may result from the corpus limitations (lack of representativeness and high synonymity against descriptive terms).

In Phase IV, sense frames are clustered. The clusters of sense frames are called senses. Optionally senses can be labelled with some descriptive terms.

Summarizing, SnS has the following features:

- it is able to find infrequent, dominated senses;
- the number of discovered senses does not have to be predefined by the user;
- it is determined solely by the content of textual corpus;
- it provides multi-level hierarchies of senses; 
- its quality outperforms the existing state-of-the-arts methods in most cases;
- it is efficient, and it can be used on the fly in the end-user oriented systems;

##### References

[1]: Kozlowski, M., Rybinski, H.: Sns: A novel word sense induction method. In: Proceedings of Rough Sets and Intelligent Systems Paradigms: Second International Conference. (2014)

[2]: Zaki, M., Hsiao, C.: Charm: An efficient algorithm for closed itemset mining. In:
Proceedings 2002 SIAM Int. Conf. Data Mining. (2002)