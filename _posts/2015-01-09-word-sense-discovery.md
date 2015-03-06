---

layout: post
title: "Word Sense Discovery"
description: "Short introduction to word sense discovery"
category: [NLP]
tags: [NLP, Text Mining]

---

The exponential growth of the Internet community brought to the production of a vast amount
of unstructured data, such as web pages, blogs, social media etc. Such mass of information
is unlikely to be analyzed by humans, so there is a strong drive to develope automatic methods capable to retrieve knowledge from the Web. Traditional techniques for text mining show theirs limit when they are applied to such huge collections of data. Most of currently used approaches are based on lexico-syntactic analysis of text, and they are mainly focused on words occurrences. Two main flaws of the approach are: inability to identify documents using different wordings, lack of context-awareness, which leads to retrieval documents which are not pertinent to the user needs. Text disambiguation can potentially provide new opening for web search engines, contributing in the realization of the Semantic Web.

In modern text mining and information retrieval, knowing the exact meaning of a word in a query becomes an important issue. Knowledge of an actual meaning of a polysemous word can significantly improve the quality of the information retrieval process by means of retrieving more relevant documents or extracting relevant information from texts. The importance of polysemy problem becomes much clearer when we look at the statistics for the English language. As stated in [1] about 73% of words in common
English are polysemous, the average number of senses per word for all words found in English
texts is about 6,5 [2]. These figures prove that there is a great need for efficient methods
enabling the disambiguation of words.

To illustrate the problem let us consider the words *apple*, *bass*. *Apple* can be used as a
software company name, personal computer, fruit, or a geographic name e.g. for river. *Bass*
may refer respectively to low-frequency tones, instrument, human voice type or fish species.
Just those few examples illustrate how various meanings can be associated with a word.

Human-beings usually do not have problems with polysemy, they catch context on the fly.
Machines are not so efficient in language processing tasks. Computers have to process unstructured textual information and transform them into data structures. The process of meaning identification for words in a context is called *Word Sense Disambiguation* (WSD). WSD is a computational process, which aims to identify the meaning of words in a context in textual data. It is subject of research in the area of computational linguistics. A comprehensive survey in this respect can be found in [3].

It is worth-noting that since the very first works on artificial intelligence the sense disambiguation is considered as a main bottleneck in developing practical AI applications. First, the WSD problem is coherently related to various issues dealing with attempts to formalise word sense representation, which is in contrast of the nature of a language. It refers particularly to granularity of senses, domain-oriented versus unrestricted nature of text, size of phrases to disambiguate (one target word, or all words). In addition it is impossible to provide a generalized method for all possible situations where WSD can be applied. This is why various WSD approaches are needed as they rely on repository types (by means of its size and content), and available knowledge resources (ontologies, dictionaries, labeled or unlabeled corpus of texts). Especially important role in WSD play dedicated knowledge resources. However, manual creation of knowledge resources is an expensive and time-consuming task. Additionally, the resources have to be subject of perpetual updates, following changes in the domain development.

The above mentioned problem is called the knowledge acquisition bottleneck. Clearly,
because of the scale of language processing algorithms, the supervised machine learning algorithms, well-known in AI, can not be effectively applied in solving language-related problems. Unsupervised methods seem to be the only chance to overcome the knowledge acquisition bottleneck. There are some research showing that some unsupervised methods are able to induce word senses from input text by clustering word occurrences, and then classify new instances into the induced clusters. The research on those methods is commonly known as *Word Sense Induction* area (WSI). It is a subproblem strictly related to Word Sense Disambiguation Task, aiming at building inventory of senses either for terms in ontologies, thesauri or dictionaries. More specifically, WSI is the task of automatic identification senses from textual repositories, without using handcrafted resources or manually annotated data. Summarizing, *Word Sense Induction* (WSI) can be perceived as a clustering problem, where words are grouped into non-disjoint clusters according to their meanings. Each cluster can be considered a separate sense of the word. 

**References**

[1]: G. Miller, M. Chadorow, S. Landes, C. Leacock, and R. Thomas. Using a semantic concordance for sense identification. In Proceedings of the ARPA Human Language Technology
Workshop, pages 240–243, 1994.

[2]: R. Mihalcea and D. Moldovan. Automatic generation of a coarse grained wordnet. In
Proceedings of NAACL Workshop on WordNet and Other Lexical Resources, Pittsburgh,
June 2001.

[3]: R. Navigli. Word sense disambiguation: A survey. ACM Computing Surveys, 41(2), 2009.
