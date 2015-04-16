---
layout: post
title: "Automating sentiment analysis 3"
description: "This is third post in four post series about Automating Sentiment Analysis. In this part we discuss algorithm used to assess text sentiment."
category: [NLP]
tags: [Java, Morfologik, Sentiment Analysis]
---

This is third post in four post series about Automating Sentiment Analysis. In this part we discuss algorithm used to assess text sentiment.

<!--more-->
Having prepared sentiment dictionary, we can use it to assess sentiment of any text. This process consists of reading analyzed text word by word, and analyzing stems found in a text, not only to check their base sentiment from sentiment dictionary, but also to check their relation to nearby stems, and see how they interact with each other. Procedure `getEmotionVector`

```java
public EmotionVector getTextEmotionVector(String commentText) {
...
}
```

returns `EmotionVector` that contains various information about texts:

```java
public class EmotionVector {
    private double cS;
    private double emotion;
    private double length;
    private double hits;
    private double positiveHits;
    private double negativeHits;
    private double strongestNegative;
    private double strongestPositive;

    ...
}
```

Code is described in details in subsections below.


####Parsing text
Text is divied into words using `,` and ` ` as separators and are then analyzed one by one by next part of algorithm.

```java  
for (String word : sentence.toLowerCase(new Locale("pl")).split("[\\s\\,]+")) {
  ... //Analysis loop
}
```

####Analysis
For each found stem, we detect if stem `stemmedWord` is some kind of special stem using Morfologik based tag retrieval. Special stems change meaning of either whole sentence or next stem and bigrams. Below is list of all detected special stems, detected by checking `stemmedTag` string contents:

- *Tags "verb"* - detecting stem with tags containing "verb" that stem is a verb.
- *Tags "adj" and "com"* - detecting stem with tags containing "adj" and "com" means that stem is comparative adjective.
- *Tags "adj" and "sup"* - detecting stem with tags containing "adj" and "sup" means that stem is superlative adjective.
- *Tags "adj"* - detecting stem with tags containing "adj" that stem is a adjective.

We also check for two special stems that are detected by 'stemmedWord' content:

- *Gradation stem* - detecting gradation stem chenges gradation of adjective or adverb following it.
- *Negation stem* - detecting negation stem changes sentiment of all stems following it to negative (from positive) or less negative.

```java
String stemmedWord = getStemmedWord(word);
String stemmedTag = getStemmedWordTag(word);

sentimentPushed = false;
foundNcBigram = false;
foundBigram = false;
if (stemmedWord != null && stemmedWord.equals("nie")) negateFound = true;
else if (stemmedWord != null && stemmedWord.equals("bardzo")) supreAdjective = true;
else if (stemmedTag != null) {
    if (stemmedTag.contains("verb")) M = 1.5D;
    else if (stemmedTag.contains("com") && stemmedTag.contains("adj")) M = 2D;
    else if (stemmedTag.contains("sup") && stemmedTag.contains("adj")) M = 3D;
    else if (stemmedTag.contains("adj")) {
        if (supreAdjective) {
            supreAdjective = false;
            M = 3D;
        } else
            M = 1.5D;
    } else M = 1D;
    if (stemmedTag.contains("adv") || stemmedTag.contains("adj")) {
        pushSentiment = 1;
    }
    supreAdjective = false;
} else {
    M = 1D;
    supreAdjective = false;
}
```

####Calculate stem sentiment value
Calculating sentiment value is divided into two parts - calculation of stem sentiment value, and calculation of sentiment value of bigrams and non-connected bigrams.

Calculating stem and bigrams sentiment value takes into account information obtained during analysis. We also store information about number of stems with positive final sentiment `tW` in variable `pH` and negative final sentiment in variable `nH`.

```java
double tW = 0D;
T = sentimentMap.get(stemmedWord);
if (negateFound) {
    if (T > 0) T = T * -1;
    else T = T * 0.6;
}
if (pushSentiment == 2) {
    pushSentiment = 0;
    if (pushSentimentValue * T > 0) {
        M = M * 1.2;
    } else {
        M = M * 0.6;
    }
    T = T + pushSentimentValue * 0.1;
    sentimentPushed = true;
    pushSentimentValue = 0D;
}
if (pushSentiment == 1) {
    pushSentiment = 2;
    pushSentimentValue = T;
}
tW += 1D * T * M;
H = H + 1D;


bigram = String.format("%s %s", bigramWord, stemmedWord);
if (sentimentMap.containsKey(bigram)) {
    bT = sentimentMap.get(bigram);
    if (negateFound) {
        if (bT > 0) bT = bT * -1;
        else bT = bT * 0.6;
    }
    tW += 0.4D * bT * M;
    foundBigram = true;
}
ncBigram = String.format("%s %s", ncBigramWord, stemmedWord);
if (sentimentMap.containsKey(ncBigram)) {
    ncbT = sentimentMap.get(ncBigram);
    if (negateFound) {
        if (ncbT > 0) ncbT = ncbT * -1;
        else ncbT = ncbT * 0.6;
    }
    tW += 0.4D * ncbT * M;
    foundNcBigram = true;
}
W += tW;
if (tW >= 0) pH++;
else nH++;
if (tW > pP) pP = tW;
if (tW < nP) nP = tW;
```

####Calculate text sentiment value and Sentiment Vector
To get final sentiment value for text `commentText`, we add stem sentiment value `tW` calculated for each stem to `W`. We also construct and return EmotionVector `vector`, which will be used in next part.
