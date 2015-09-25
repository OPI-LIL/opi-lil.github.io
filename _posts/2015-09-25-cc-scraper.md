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
Scraper architecture consists of several components:

* Main Server
* Cassandra Storage
* Common Crawl Storage Servers

####Main Server

Main Server &minus; it's a core of the system, on which scraper performs its tasks and language detection takes place. It is an abstract term since it is built up from a few computers stored in a rack and makes the environment for scraper existence. Currently, the computational resources available within the main server are: Intel(R) Xeon(R) CPU E5-2420 v2 @ 2.20GHz 16 cores and 384 GB RAM. However, the main server is expected to be enlarged significantly in the near future.

Since the enormous data volume, scraping activities take place every day after working hours in the Institute ![smile]({{site.url }}/assets/images/smile.png) (After scraping actors are launched, the Internet connection quality is much reduced.) Crontab scripts take care about both firing scraping up and turning it down.

Highly concurrent processing of Web Data Crawl goes on. As we have used actor based Akka Framework we got it out of the box.

The main server is a habitat for Actors responsible for downloading and processing data, and for Polish language detection.

Actors which are involved in processing:

* ActorSystem which plays the role of application CEO 
* Cassandra Storage
* Common Crawl Storage Servers


![Scraper Architecture]({{ site.url }}/assets/images/scraper-architecture.png)

