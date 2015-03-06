---

layout: post
title: "Clustering Search Results using Word Sense Induction"
description: "Clustering Snippets using as clusters induced senses"
category: [NLP]
tags: [NLP, Text Mining]

---

The goal of text clustering in information retrieval is to discover groups of semantically related documents. Contextual descriptions (snippets) of documents returned by a search engine are short, often incomplete, and highly biased toward the query, so establishing a notion of proximity between documents is a challenging task that is called Search Result Clustering (SRC). 

<!--more-->

The general flow of search result clustering can be described in three main steps [1]:

1. Given a query q, a search engine is used to retrieve a list of results (snippets)
R = (\\(r_1,\ldots,r_n\\));
2. A clustering C = (\\(C_0,C_1, \ldots, C_m\\)) of the results in R is obtained by means
of a clustering algorithm;
3. The clusters in C are optionally labeled.

Approaches to search result clustering can be classified as data-centric or
description-centric [2]. The data-centric approach focuses more on the problem of data clustering, rather than presenting the results to the user. Scatter/Gather is an example, which divides the dataset into a small number of clusters and, after the selection of a group, performs clustering again and proceeds iteratively using the Buckshot-fractionation algorithm.
Other data-centric methods use hierarchical agglomerative clustering [3] that replaces single terms with lexical affinities (2-grams of words) as features, or exploit link information. Description-centric approaches are more focused on the description that is produced for each cluster of search results. This problem is also called descriptive clustering: discovery of diverse groups of semantically related documents associated with a meaningful, comprehensible and compact text labels. Accurate and concise cluster descriptions (labels) let the user search through the collection’s content faster and are essential for various browsing interfaces. The task of creating descriptive, sensible cluster labels is difficult - typical text clustering algorithms rely on samples of keywords for describing discovered clusters. Among the most popular and successful approaches are those phrasebased, which form clusters based on recurring phrases instead of numerical frequencies of isolated terms. STC algorithm employs frequently recurring phrases as both document similarity feature and final
cluster description ([4], [5]). Clustering in STC is treated as finding groups of documents sharing a high ratio of frequent phrases, cluster descriptions are a subset of the same phrases used to form the cluster. KeySRC improved STC approach by adding part-ofspeech prunning and dynamic selection of the cutoff level of the clustering dendrogram [6].

The SnS-based clustering method (called further SnSRC) is performed in two phases: first, simultaneously during sense induction, and then after sense discovery for those results that remained not grouped. The first phase is based on the process of frequent termsets mining. Discovered closed frequent termsets have support, and list of results, in which they appear. Senses are grouped sense frames. Each sense frame has the main contextual pattern, so according to sense frames the snippets containing the main pattern are grouped in the corresponding result cluster. Summarizing, for each sense (having sense frames) a corresponding cluster of snippets is constructed. Let us note that after this phase is
completed, there may remain snippets which are non-grouped.
In the second phase, non-grouped snippets are tested iteratively against each
of the induced sense. Now, the clustering of remaining snippets consists in using
the bag-of-words representation and a simple similarity measure between the snippets
representations and sense clusters (it calculates the size of intersection between the
bag-of-words of snippet and the set of words, obtained as the union of all content words of contextual patterns building sense frames clustered in the given sense).

We conducted broad range of experiments with the SnSRC on the union of two
tests sets AMBIENT [[8]] and MORESQUE [[9]]. SnSRC was compared with such search results clustering methods: (1) Lingo [7], (2) Suffix Tree Clustering [4-5], (3) KeySRC (the state-of-the-art search result clustering method) [6].

We compared SnSRC and above SRC systems against three baselines: (1) Singletons (each snippet is clustered as a separate singleton), (2) All-in-one (all the snippets are clustered into a single cluster), (3) Wikipedia (snippet is added to the cluster corresponding to the best-matching Wikipedia page representing a meaning).
Clustering evaluation problem is a difficult issue, and one for which there
exists no unequivocal solution. Many evaluation measures have been proposed
in the literature so, in order to get exhaustive results, we calculated three dis-
tinct measures, namely: Adjusted Rand Index (ARI), Jaccard Index (JI) and F1
measure [10].
We reported the results of clustering quality for SnSRC in the below table,
compared against systems (i.e., Lingo, STC, and KeySRC) and three baselines
(i.e., all-in-one, singleton, and Wikipedia).

| Algorithm        | ARI           | JI  |     F1 |
| ------------- |:-------------:| -----:| --------:|
| Lingo     | -0.53 | 36.36 |   16.73   |
| STC      | -7.90  |  38.23 | 14.96 |
| KeySRC | 14.34    |   27.77 | 63.11|
| **SnSRC** | **14.04** | **22.74**  | **60.16** |
|All-in-one | 0.00 | 47.12 | 42.40 |
| Singleton | 0.00 | 0.00 | 68.17 |
|Wikipedia | 13.83 | 56.02 | 14.33 |

In above table there were reported all quality measures acquired during evaluations on AMBIENT+MORESQUE data set according to results from [10]. An initial finding here is that the SnSRC and KeySRC report very good clustering quality measures. We note that although KeySRC outperforms the other SRC descriptive-centric algorithms in terms of ARI and F1, it attains very low JI results. The same characteristics is connected with the SnSRC method. SnSRC
reports similar measures to KeySRC, especially comparing them with results of
Lingo and STC. According to baselines, the singleton baseline produces trivial,
meaningless clusterings, as measured by ARI and JI. The all-in-one baseline obtains non-zero JI, its F1 is lower than singleton, because of its lower recall. The Wikipedia baseline fares well compared with the other baselines in terms of ARI and JI, but achieves lower F1, again because of low recall. Finally, KeySRC and SnSRC consistently outperforms the other SRC systems in terms of ARI and F1.

##### References

[1]: Roberto Navigli, Giovanni Crisafulli. Inducing word senses to improve web search result clustering. In: Proceedings of EMNLP 2010. (2010)

[2]: Claudio Carpineto, Stanislaw Osinski, Giovanni Romano, and David Weiss. A survey of web clustering engines. ACM Computing Surveys 41(3), pp. 1–38. (2009)

[3]: Israel Maarek, Ron Fagin and Dan Pelleg. Ephemeral document clustering for web applications. IBM Research Report RJ 10186. (2000)

[4]: Oren Zamir and Oren Etzioni. Web document clustering: A feasibility demonstration. In Proceedings of the 21st Annual International ACM SIGIR Conference
on Research and Development in Information Retrieval, pp. 46–54. (1998)

[5]: Oren Zamir and Oren Etzioni. Grouper: a dynamic clustering interface to Web search results. Computer Networks, pp. 1361–1374. (1999)

[6]: Andrea Bernardini, Claudio Carpineto, and Massimiliano DAmico. Full-subtopic retrieval with keyphrasebased search results clustering. In Proceedings of 2009 IEEE/WIC/ACM International Conference on Web Intelligence, pp. 206–213. (2009)

[7]: Osinski, S., Weiss, D.: A concept-driven algorithm for clustering search results.
IEEE Intelligent Systems. (2005)

[8]: http://credo.fub.it/ambient/

[9]: http://lcl.uniroma1.it/moresque/ 

[10]: Di Marco, A., Navigli, R.: Clustering and diversifying web search results with
graph-based word sense induction. Computational Linguistics. (2013)