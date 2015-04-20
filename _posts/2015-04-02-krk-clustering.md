---
layout: post
title: "KRK-based study field's search engine"
description: "Study fields described by KRK vectors are measures in order to find similar ones"
category: [NLP]
tags: [NLP, Text Mining]
---

Idea of European Qualification Frames orignated in 2004 and it was the response for the reported demands of EU countries. The goal was to create a transparent system of qualifications/competences, which enables to compare graduates from various universities within the same country, or between countries. In 2008 European Parlament adopted regulation about European Qualification Frames.  In Poland those qualification frames are called shortly KRK competences.

<!--more-->
KRK competences are the specialized description of the abilities/qualifications, which students gain after graduation from the given study field.

In Polish Higher Education System each study field has assigned the KRK competences (described in the semi-structured file). It means that study fields are described by the file pdf/doc as in the example below. Polish KRK competence files refers to  3000 study fields.

![KRK competences described in a semi-structured file]({{ site.url }}/assets/images/krk-sample-file.png)

During our research we have conducted: 

 - clustering semi-structured documents - namely documents describing study field's KRK competences; 
 - search engine building - finding similar study fields in the KRK space model.

The applied method enables extracting and processing KRK competences from diverse types of semi-structured documents. It consists of two stages: (1) entity extraction from documents (building vectors of KRK competences for each study field), (2) measure the similarity between KRK vectors and optionally (2) clustering study fields using those competence representations.  The results allow to compare and identify similar study fields according to theirs final effects of education.

Some working example of KRK search engine prototype is placed below.

![Study fields retrieved for the economy, Warsaw University]({{ site.url }}/assets/images/krk-search-engine.png)

###References

[1]: [Link to the KRK search engine prototype](http://uns-platforma-test.opi.org.pl/krk/)

[2]: [Link to KRK info site](http://biol-chem.uwb.edu.pl/new/pliki/pdf/krajowe%20ramy%20kwalifikacji%20krok%20po%20kroku%20nieoficjalne.pdf)

