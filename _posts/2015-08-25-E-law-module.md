---
layout: post
title: "E-law Module Supporting Lawyers in the Process of Knowledge Discovery from Legal Documents"
description: "E-law module is the web application, which works mainly as the set of information retrieval and extraction tools dedicated for the lawyers."
category: [NLP]
tags: [NLP, Text Mining]
---


E-law module is being built by the CTI (Center of Information Technologies for the Social Science) consortium, which is granted with the European funds. The main goal of the consortium is to build innovative hardware and software infrastructure for lawyers, sociologists, psychologists and other humanists. The consortium consists of three members: Cardinal Stefan Wyszynski University in Warsaw, Military Institute of Aviation Medicine, and National Information Processing Institute (OPI).

<!--more-->

OPI as a member of the consortium is responsible for delivering software infrastructure, namely three modules: (1) [E-law](http://eprawo-test.opi.org.pl/) - module supporting lawyers with text mining functionalities e.g. as classifiers, machine translation or search engines, (2) [E-survey](http://esurvey-test.opi.org.pl/) - module responsible for creating questionnaires in a drag-and-drop wizard mode and sending them to the respondents (the target group is randomly selected using sampling algorithms), (3) [E-analytics](http://eanalytics-test.opi.org.pl/) – module supporting social science researchers in performing the qualitative analysis for various heterogenic data (statistical tools, predicative and simulation methods).

E-law module consists of the following tools: (1) document search engine; (2) context oriented search engine plugin; (3) legal phrase oriented machine translation; (4) document meta-tagger; (5) verdict finder.

#### Document Search Engine
During the project, 2 million legal documents have been downloaded from various, Polish and foreign European, open databases. Only the metadata about documents were collected: title, summary, depositors, date, keywords etc. The search engine retrieves results using Apache Lucene index and well defined filters. For building the Lucene index, different analyzers depending on the language were used. Morfologik analyzer was used for Polish, Standard analyzer was used for other languages.

#### Context Oriented Search Engine Plugin
We expanded our search engine with the plugin, which finds all relevant contexts for the query and cluster results (documents) according to the contexts. In the module presented, a novel result clustering method has been introduced, which exploits rule association mining in order to create coherent clusters of results concerning different subtopics. The core part is a frequent term sets mining method identifying closed frequent termsets using CHARM algorithm [1]. Discovered frequent termsets are hierarchized and used for building labeled trees of patterns.

#### Legal Phrase Oriented Machine Translation
One of the key features of E-law module was to aid law-related people with translating legal phrases from Polish to English and vice-versa. The created translation system uses parallel bilingual data (Polish and English). The total amount of Polish-English data is approximately 42.000.000 pairs of words, phrases, sentences and whole documents (different granularity), incorporated from sources e.g., EUPARL, TED.

The process starts from the data alignment based on the PoS oriented floating window of the correspondent block of text. Next, there is processed final translation as follows: 

 1. Input phrase split by tokenizer into n-grams of the predefined maximum size;
 2. Each n-gram is taken as a query to Lucene index and corresponding result text block is narrowed down using data alignment method;
 3. Each result block is processed by the tokenizer (point 1) and stored in a sorted list;
 4. Translation uses replacement by most frequent ngrams, starting from the longest n-grams.

Presented solution cannot compete against currently working SMT solutions like Joshua and Moses (up to 0.20 higher BLEU than the described solution [2]). Although the simplicity and little amount of RAM necessary makes this approach useful.

#### Document Meta-Tagger
Document Meta-Tagger is a tool, which assigns the high-level keywords to the text using the external knowledge resources i.e., BabelNet. [BabelNet](http://babelnet.org/) is both a multilingual, encyclopedic dictionary with lexicographic and encyclopedic coverage of terms and a semantic network, connecting concepts and named entities in a very large network of semantic relations, called Babel synsets. Each BabelNet synset represents a given meaning and contains all the synonyms, which express that meaning in a range of different languages. BabelNet 3.0 covers and is obtained from the automatic integration of Wikipedia, WordNet, Wiktionary and Wikidata [3]. The meta-tagger presented works on BabelNet synsets. It performs tokenization as the first step, removing stop-words, lower-casing, lemmatization and PoS tagging. We only persist the noun-phrases, because there are the most informative ones. Next we use the BabelNet API in order to disambiguate phrases. The result of the disambiguation step is the most probable synset. Each synset has it’s categories (like Wikipedia categories describing articles). Within the text, all synsets are gathered and the most frequent categories of the synsets are retrieved as the meta-tags.

#### Verdict Finder
This tool refers to information extraction (IE). IE deals with unstructured or semi-structured machine-readable documents. The most popular tasks in IE are: named entity recognition, coreference and relationship identification, table extraction or the terminology extraction. In the legal judgments we are interested in extracting article’s legal numbers, which were used as the law references.

The information extraction process is performed as follows: 
 1. Judgments processing using Apache Tika;
 2. Article’s legal numbers extraction using regular expressions, which come from the retrieved content files.

For each document the vector of legal article’s numbers is build. Such vector representation is used in order to find similar verdicts. Similarity between vectors is measured by the Jaccard metric. The 10 most similar ones are returned as the potentially similar judgments.

###References
[1]: M. Zaki and Ch. Hsiao. 2002. CHARM: An efficient algorithm for closed itemset mining. Proceedings 2002 SIAM Int. Conf. Data Mining, pages 457–472. Arlington.
[2]: P. Koehn. 2005. Europarl: A parallel corpus for statistical machine translation. MT Summit, pages 79–86.
[3]: R. Navigli and S. Ponzetto. 2012. The Automatic Construction, Evaluation and Application of a Wide Coverage Multilingual Semantic Network. Artificial Intelligence, pages 217–250.