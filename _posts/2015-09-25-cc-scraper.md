---
layout: post
title: "Smashing BigData! – Our Concurrent Scraper written in Scala/Akka Framework"
description: "Smashing BigData! – Our Concurrent scraper written in Scala/Akka Framework for processing of Common Crawl Web Data"
category: [NLP]
tags: [NLP, Text Mining, Web Crawler, Scala, Akka Framework]
---

###Motivation
We are given a task – to construct N-grams for Polish language based on Polish Internet Websites. One could say: “It is hardly possible to be done”, because first you have to scrape all the Internet content consisting of nearly petabytes of data, such storage would be very demanding and challenging i.e. expensive infrastructure by the means of hardware and software, a need of well-trained professionals for managing etc. 
Fortunately, somebody has already done this tremendous task! [The Common Crawl Foundation](http://commoncrawl.org/) comes forward with its Web Data Crawl. 

<!--more--> 

###BigData Source
Now, a few words about Common Crawl Project. It is a non-profit foundation registered in California for producing and maintaining open repository of web crawl data which is widely accessible and analyzable. They build up very useful resource for innovative research, and for applications in business and education.

Web Data Crawl has being collected over 7 years, the project has evolved over the years, with some changes in data formats and metadata used. The Data is divided into segments what comes naturally, since fairly two years the data dump is done once a month. Each segment can extent to hundreds terabytes of data (including all formats). The Common Crawl Foundation provides a few formats for their data. We have chosen a WET format, as our task only requires textual information. WET files contain only plaintext without any markup tags. At the top, there is meta information which is followed by website content. More details are available [here](http://commoncrawl.org/the-data/get-started).

As we would like to process the Common Crawl Web Data, we have to get this crawl at first! Since we would like to skip using Amazon S3, we have to download the data by ourselves (and that's why we need a scraper).  

###Scraper Architecture 

![Comments view]({{ site.url }}/assets/images/scraper-architecture.png)

