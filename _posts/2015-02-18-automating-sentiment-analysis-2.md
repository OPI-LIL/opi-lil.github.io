---
layout: post
title: "Automating sentiment analysis<br> part 2"
description: "This is second post in four post series about Automating Sentiment Analysis. In this part we discuss automating building sentiment dictionary."
category: [Sentiment Analysis]
tags: [Twitter, Java, Morfologik, Sentiment Analysis]
---

Building dictionary
-----------

> This is second post in four post series about Automating Sentiment Analysis. In this part we discuss automating building sentiment dictionary.

<!--more-->

Having obtained sentiment annotated data (process is described in previous post), we can build sentiment annotated word lexicon. This process consists of two steps - gathering all meaningful tokens (in form of stems) existing in sentiment annotated texts and calculating final sentiment score of each stem.

stem
  : stems are base forms of word, that is common to al its inflected variants. [1]

Below we discuss each part of process, with code samples and descriptions. Full code and sample project using our `SentimentAnalyzer` class will be available on BitBucket.
####Preparation
Before we begin, we must prepare data files gathered in previous step. This process was described in previous post.
####Acquiring tokens
To acquire tokens, we needed to parse all text data gathered in previous step to form that will be useful for building dictionary.  

```java
for (String sentence : sentenceBase.toLowerCase(new Locale("pl")).split("[\\?\\!\\.]+")) {
    for (String word : sentence.toLowerCase(new Locale("pl")).split("[\\s\\,]+")) {
        word = word.trim();
        String stemmedWord = getStemmedWord(word);
        if (word.length() < 3) {
            ncBigramWord = bigramWord;
            bigramWord = null;//There are no meaningful 2 letter words in Polish language
        } else if (ignoreWords.containsKey(stemmedWord)) {
            ncBigramWord = bigramWord;
            bigramWord = null;
        } else if (word.startsWith("@")) {
            ncBigramWord = bigramWord;
            bigramWord = null; //skip replies, retweets and so on
        } else if (stemmedWord != null) {
            sentimentWordDatabase.putIfAbsent(stemmedWord, new AtomicLong(0));
            sentimentWordDatabase.get(stemmedWord).incrementAndGet();
            if (positive) {
                positiveWordDatabase.putIfAbsent(stemmedWord, new AtomicLong(0));
                positiveWordDatabase.get(stemmedWord).incrementAndGet();
            } else {
                negativeWordDatabase.putIfAbsent(stemmedWord, new AtomicLong(0));
                negativeWordDatabase.get(stemmedWord).incrementAndGet();
            }
            if (bigramWord != null) {     //Saving bigram
                bigrams.putIfAbsent(String.format("%s %s", bigramWord, stemmedWord), positive);
            }
            if (ncBigramWord != null) {     //Saving non connected bigram
                bigrams.putIfAbsent(String.format("%s %s", ncBigramWord, stemmedWord), positive);
            }
            ncBigramWord = bigramWord;
            bigramWord = stemmedWord;
        }
    }
}
```

Code snippet above is responsible for saving all tokens (stems) from texts, which are not in IngoreWords list, to two maps `sentimentWordDatabase` and, depending on text annotated sentiment `positiveWordDatabase` if sentiment is positive and `negativeWordDatabase` if sentiment is negative. Stem occurence is saved increment of value stored in map, as shown in a part below

```java
negativeWordDatabase.putIfAbsent(stemmedWord, new AtomicLong(0)); //Put stemmedWord with value 0 in ConcurrentMap
negativeWordDatabase.get(stemmedWord).incrementAndGet(); //Increment value stored in map, using AtomicLong functionality
```

It also saves bigram and non connected bigram to appropriate collections. We used this function to parse all files gathered from Twitter to build largest possible dictionary.

Function used to get tokens (stems) `getStemmedWord(String wordData)` is described below. In addtition to retrieving stem from word, it can, if stem is not found (meaning word is not correct) search for possible replacements and get stem for first replacement. Stemmer used in our code is based on [Morfologik project](http://morfologik.blogspot.com/).

```java
public string getStemmedWord(String wordData) {
  Speller speller = new Speller(Dictionary.getForLanguage("pl"));
  PolishStemmer stemmer = new PolishStemmer();
  List<WordData> wordList = stemmer.lookup(wordData);
  if (wordList.size() != 0) {
      WordData wd = wordList.get(0);
      return wd.getTag().toString();
  } else {
      try {
          List<String> replacements = speller.findReplacements(wordData);
          if (replacements.size() > 0) {
              List<WordData> innerList = stemmer.lookup(replacements.get(0));
              if (innerList.size() != 0) {
                  WordData wd = innerList.get(0);
                  return wd.getTag().toString();
              }
          } else {
              return null;
          }
      } catch (Exception e) {
      }
  }
  return null;
}
```

>**Note**
>
>Morfologik is a stemming tool designed for Polish language only. There exists such tools for other languages, but using them is out of scope of this post.

####Calculating final token sentiment

Having collected all tokens exisiting in analyzed texts, we can generate final token sentiment score. We used same sentiment score calculation method used by Kiritchenko *et al* [2] Sentiment score for token $t$ was defined as
$$s(t) = PMI(t,positive) - PMI(t,negative)$$
where PMI stands for pointwise mutual information, and is defined as
$$PMI(t,positive) - \log_2 \frac{freq(t,E_1) * count(W)}{freq(t,W),freq(t,E_{-1})}$$
where $freq(t,E_1)$ is the number of times token $t$ occurs in collection $E_1$ and $count(W)$ is number of different tokens $t$ in collection $W$. $freq(t,E_{-1})$, $freq(t,W)$ are described in similar way, as is $PMI(t,negative)$. Equation can be simplified to:
$$s(t) = \log_2 \frac{freq(t,E_1)*count(E_{-1})}{freq(t,E_{-1})*count(E_1)}$$
These equations are executed in code in form shown below. In code, collection $E_{-1}$ was represented by `negativeWordDatabase` map, collection $E_1$ by `positiveWordDatabase` map, $W$ by `sentimentWordDatabase` map and token $t$ by variable `String name`.

```java
public void buildSentimentDictionary() {

    System.out.println(String.format("Building sentiment dictionary..."));
    System.out.print(String.format("Analyzing words..."));
    for (String name : sentimentWordDatabase.keySet()) {
        Double freq_w_pos = 0.0001D;
        Double freq_w_neg = 0.0001D;
        if (positiveWordDatabase.containsKey(name))
            freq_w_pos = positiveWordDatabase.get(name).doubleValue();
        if (negativeWordDatabase.containsKey(name))
            freq_w_neg = negativeWordDatabase.get(name).doubleValue();

        Double freq_pos = Double.valueOf(positiveWordDatabase.size());
        if (freq_pos == 0D) freq_pos = 1D;
        Double freq_neg = Double.valueOf(negativeWordDatabase.size());
        if (freq_neg == 0D) freq_neg = 1D;
        Double sentimentScore = Math.log(freq_w_pos * freq_neg / (freq_w_neg * freq_pos)) / Math.log(2);
        sentimentMap.put(name, sentimentScore);
    }
    System.out.println("done.");
...
}
```

This code takes into account fact that the stem may not appear in both collections, `positiveWordDatabase` and `negativeWordDatabase` and produces good results. Very similiar code is used for generating sentiment score for bigrams and non-connected bigrams.

####Wrapping it up in code

After looking at how the process work, we can write simple tool for automated dictionary generation. Below is show a simple code of such tool. Full sample project will be available on BitBucket soon.
```java
Map<String,Boolean> dictionaryFiles = new HashMap<>();
dictionaryFiles.put("positive.json",true);
dictionaryFiles.put("negative.json",false);
AnalyzerProgramOptions options = new AnalyzerProgramOptions();
options.setBuild(true);
SentimentAnalyzer analyzer = new SentimentAnalyzer(options);
analyzer.loadIgnoreWords();
analyzer.loadDictionaryBuilderFiles(dictionaryFiles)
analyzer.buildSentimentDictionary()
analyzer.writeSentimentDictionary("dictionary.dat")
```

This code will set two files to use for dictionary generation, one with positive texts and one with negative, set program options, and after loading ignore words list, will load data from selected filed, parse them and save generated dictionary to file `dictionary.dat`

Antoni Sobkowicz 2015

####References
[1]: 	Word Stem; http://en.wikipedia.org/wiki/Word_stem	

[2]: S. Kiritchenko, X. Zhu and S. M. Mohammad (2014) "Sentiment Analysis of Short Informal Texts",	https://www.jair.org/papers/paper4272.html