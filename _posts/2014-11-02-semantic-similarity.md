---
layout: post
title: "Cross-level Semantic Similarity"
description: "Semantic Textual Similarity measured using knowlege-poor approach"
category: [NLP]
tags: [NLP, Text Mining]
---

In this post, we describe the OPI system participating in the Semeval-2014 task 3 Cross-Level Semantic Similarity. Our approach is knowledge-poor, there is no exploitation of any structured knowledge resources as Wikipedia, WordNet or BabelNet. The method is also fully unsupervised, the training set is only used in order to tune the system. System measures the semantic similarity of texts using corpus- based measures of termsets similarity

<!--more-->

The task Cross-Level Semantic Similarity of SemEval-2014 aims at an evaluation for semantic similarity across different sizes of text (lexical levels). Unlike prior SemEval tasks on textual similarity that have focused on comparing similar-sized texts, the mentioned task evaluates the case where larger text must be compared to smaller text, namely there are covered four semantic similarity comparisons: paragraph to sentence, sentence to phrase, phrase to word and word to sense. We present the method for measuring the semantic similarity of texts using a corpus-based measure of termsets (set of words) similarity. We start from preprocessing texts, identifying boundary values, computing termsets similarities and derive from them the final score, which is normalized.

The input of the task consists of two text segments of different level. We want to determine a score indicating their semantic similarity of the smaller item to the larger item. Similarity is scored from 0 to 4, when 0 means no semantic intersecion, 4 means that two items have very similar meanings.

Related work can be classified into four major categories: vector-based document models methods, corpus-based methods, knowledge-based methods and hybrid methods [1].

**Vector-based** document models represent document as a vector of words and the similarity evaluation is based on the number of words that occur in both texts. Lexical similarity methods have problems with different words sharing common sense. Next approaches, such as corpus-based and knowledge-based methods, overcome the above issues.

**Corpus based** methods apply scores provided by Pointwise Mutual Information (PMI) and Latent Semantic Analysis (LSA). The Pointwise Mutual Information (PMI) [2] between two words $w_i$ and $w_j$: 

\begin{equation}PMI(w_i,w_j) = log_2\frac{p(w_i,w_j)}{p(w_i)p(w_j)}\end{equation}

The Latent Semantic Analysis (LSA) [3] is a mathematical method for modelling of the meaning of words and contexts by analysis of representative corpora. It models the meaning of words and contexts by projecting them into a vector space of reduced dimensionality, which is built up by applying singular value decomposition (SVD).

**Knowledge based** methods apply information from semantic networks as WordNet. They exploit the structure of WordNet to compare concepts. Lesk [4] defined similarity between concepts as the intersection between the corresponding glosses. Leacock and Chodorow [5] proposed metric based on the length of the shortest path between two concepts. Hughes and Ramage [6] used a semantic representation of texts from random walks on WordNet.

**Hybrid methods** use both corpus-based measures and knowledge-based measures of word semantic similarity to determine the text similarity. Mihalcea and Corley [7] suggested a combined method by exploiting corpus based measures and knowledge-based measures of words semantic similarity. 

Our system is **corpus based** and fully **unsupervised**. It exploits Wikipedia as a raw corpus for words co-occurrence estimation. The proposed method is not using any kind of textual alignment (e.g. exploiting PoS tagging or WordNet concepts). The method consists of four steps: preprocessing, identifying boundary values, termset-to-termset similarity computation, text-to-text similarity phase. The results from the text-to-text similarity phase are very often beyond the range 0-4, therefore we must normalize them. We evaluated two normalization approaches: linear normalization and non-linear one. The non-linear normalization is based on built clusters (referring to integer values from 0 to 4), which are created using training data set.

**Preprocessing**

In the first step the compared texts are retrieved, and then processed into the contexts. Context is the preprocessed original text represented as a bag of words. Texts are processed using a dictionary of proper names, name entities recognizers, PoS-taggers, providing as a result the required contexts. Contexts contain nouns, adjectives, adverbs and proper names. The output of this stage is a pair of contexts passed to the next phase.

**Identifying Boundary Values**

This phase is introduced in order to fast detect texts, which are unrelated (0 score) or very similar (4 score). Unrelated ones are identified basing on the lack of any co-occurrences between words from compared texts. It means that any pair of words from compared contexts do not appear together in any Wikipedia paragraph. The very similar texts are identified in two steps. At first we check if all words from the shorter texts are contained in the longer one. If the first check is not fulfilled we compute the second check using $c_{1,2}$ as the number of Wikipedia paragraphs that contain all of words from both contexts in the nearest neighborhood (20-words window), $c_1$ and $c_2$ as the numbers of Wikipedia paragraphs that contain contexts within 20-words window. If the ratio $\frac{c_{1,2}}{max(c_1,c_2)}$ is higher than 50% then the analyzed pair of texts refers to the same concept (very similar ones). 

**Termset-to-termset Similarity**

Termset-to-termset similarity (*t2tSim*) is defined by measure similar to PMI:

\begin{equation}t2tSim(W_i,W_j) = \frac{c(W_i,W_j)}{min(c(W_i),c(W_j))}\end{equation}

where $W_i,W_j$ are termsets and $c(X_1,..,X_n)$ is a number of Wikipedia paragraphs that contain all terms covered by termsets	$X_1,..,X_n$. Two input termsets are semantically close if the similarity measure $t2tSim$ is higher than the user-defined threshold (e.g. 10%). Comparing to the previous step we use the minimum operator in the formula’s denominator in order to take into account even one directed relevant association. It was proved experimentally [8] that the proposed measure leads to better results than the PMI measure using NEAR query (co-occurrence within a 10-words window).

**Text-to-text Similarity**

Given two input texts we compute the termset-to-termset similarities in order to derive the final semantic score. We attempt to model the semantic similarity of texts as a function of the semantic similarities of the component termsets. We do this by combining metrics of termset-to-termset similarities and weights into a formula that is a potentially good indicator of semantic similarity of the two input texts. Weights are experimentally set with linear scalable values $w_{m1}=4$, $w_{m2}=2$ and $w_{m3}=1$, respectively. The pseudo-code is placed below.

![The pseudo-code of the phases text-to-text similarity]({{ site.url }}/assets/images/cross-sts.jpg)

The crucial part of the method is a process of normalization obtained measures into the range (0,4). The values 0 and 4 are covered by the step described in the section 3.2. We need to normalize values from the text-to-text similarity phase. This step can be done in two ways: linear normalization and non-linear one. The first one is a casual transformation defined as dividing elements by theirs maximum and scaling to 4. The second one is based on clustering training set. In other words, using training set we induce rules how reported text-to-text similarity values should be transformed into the range (0,4).  We implemented hierarchical agglomerative clustering algorithm (with average linkage) in order to cluster similarity measures into five distinct groups. Sorted centroids of the above created groups are labeled with values 0 to 4 respectively. For each new similarity measure (obtained in the testing phase) we measure the distance to the closest cluster’s centroids. 

**Evaluation**

In Task 3, systems were evaluated both within one of four comparison types and also across all comparison types. The system outputs and gold standard ratings are compared in two ways, using Pearson correlation and Spearman’s rank correlation (rho). Pearson correlation tests the degree of similarity between the system’s similarity ratings and the gold standard ratings. Spearman’s rho tests the degree of similarity between the rankings of the items according to similarity. Ranks were computed by summing the correlation values across all four levels of comparisons. The sum of the Pearson correlations is used for the official rank of Task 3. 

The best results our algorithm scores in the category phrase-to-word. In this comparison type it was ranked at 12th position among 21 participating systems. In the word-to-sense it was at 14th position among 20 systems. The word-to-sense comparison is converted into the task similar to phrase-to-word by using glosses of target senses. Each key of WordNet sense is replaced with its gloss. It is the only situation when we use the external knowledge resources, but it is not a part of the algorithm. The last comparison (sentence-to-phrase) was our worst, because we did not beat the baseline, as we did in the previous categories. In the sentence-to-phrase comparison word alignment or syntax parsing seems to be very important, in our case none of them was applied. More details about the evaluation phase is in [8].

Summarizing, we present our cross-level semantic similarity method, which is knowledge-poor (not using any kind of structured resources like machine-readable dictionaries, thesaurus, or ontologies) and fully unsupervised (there is no learning phase leading to models enable to categorize compared texts). The method exploits only Wikipedia as a raw corpora in order to estimate frequencies of co-occurrences. We were aimed to verify how good results can be achieved using only corpus-based approach and not including algorithms that have embedded deep language knowledge. The system scores best in the phrase-to-word (12th rank) and word-to-sense (14th rank) types of comparison with regard to Pearson correlation. The worst results were reported in the sentence-to-phrase category, which brings us the conclusion that comparison of larger text units can not be based on bag of words approaches, where order of words is not important. 


###References
[1]: Aminul Islam and Diana Inkpen. 2008. Semantic Text Similarity using Corpus-Based Word Similarity and String Similarity. ACM Transactions on Knowledge Discovery from Data , 2(2): 1–25.

[2]: Peter Turney. 2001. Mining the web for synonyms: PMI-IR versus LSA on TOEFL. In
Proceedings of the Twelfth European Conference on Machine Learning, pages 491–502.

[3]: Thomas Landauer and Susan Dumais. 1997. A solution to Platos problem: The latent semantic analysis theory of acquisition, induction, and representation of knowledge. Psychological Review, 104: 211–240.

[4]: Michael Lesk. 1986. Automatic sense disambiguation using machine readable dictionaries: How to tell a pine cone from an ice cream cone. In Proceedings of the SIGDOC Conference , pages 24–26.

[5]: Claudia Leacock and Martin Chodorow. 1998. Combining local context and WordNet sense similarity for word sense identification. In WordNet, An Electronic Lexical Database , pages 265–283.

[6]: Thomas Hughes and Daniel Ramage. 2007. Lexical semantic relatedness with random graph walk. In Proceedings of the Conference on Empirical Methods in Natural Language Processing , pages 581–589.

[7]: Rada Mihalcea, Courtney Corley and Carlo Strapparava. 2006. Corpus-based and Knowledge-based Measures of Text Semantic Similarity. In Proceedings of the American Association for Artificial Intelligence , pages 775–780.

[8]: Marek Kozlowski. 2014. OPI: Semeval-2014 Task 3 System Description. Proceedings of the 8th International Workshop on Semantic Evaluation (SemEval 2014) , pages 454–458.