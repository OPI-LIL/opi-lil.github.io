---
layout: post
title: "AAIA'16 Data Mining Challenge: Predicting Dangerous Seismic Events in Active Coal Mines"
description: "The data mining competition"
category: [data mining]
tags: [data mining, SVN, neural networks, genetic algorithms]
---

AAIA'16 Data Mining Challenge is the third data mining competition associated with International Symposium on Advances in Artificial Intelligence and Applications (AAIA'16, https://fedcsis.org/2016/aaia) which is a part of FedCSIS conference series. This time, the task is related to the problem of predicting periods of increased seismic activity which may cause life-threatening accidents in underground coal mines. If you would like to know more about the contest, please visit: https://knowledgepit.fedcsis.org/.

<!--more-->

##Genetic Algorithm Approach

####Data normalization
This approach to data normalization assumed, that there is a pattern in time series, so every part of data which contained time series were replaced by linear approximation (ax + b), so 24h values were at the end replaced by two numbers (a, b) of approximation. At the front of vector, an encoded set of meta-data has been added. The numerical values of input vectors were rescaled to fit the range <0..1>.

####Polynomial approximation
The solution proposed assumes that there exists a polynomial equation of given degree, which can approximate training data points. Polynomial parameters found can be used further to discover some information about seismic events – vector values with non-zero polynomial parameters assigned by genetic algorithm may be considered as essential in modelling of the seismic processes.
The data assigned to the competition is not equally distributed. For ~130 000 training vectors, only ~3 000 were labelled as "warning", the rest were labelled as "normal". To overcome this, a 25% of ~3 000 of "warning" data is picked randomly (around 750 vectors), and the same number of data is then picked from "normal" cases (also 750 vectors). Each genetic epoch is presented a new randomly picked pair of "normal" and "warning" sets.  

####Populations
Each breed of polynomial genome is tested on a set of 1 500 vector data-set and has a score assigned, using mean square error measure. To avoid fluctuations from the data picking scheme, both current-generation and previous-generation scores are take into account giving the final result as mean of these two.  For every epoch, 10 best genomes were taken from previous epoch, and 10 more were generated randomly.

##Neural Networks Approach

####Data normalization
Some of features was skipped, as there was little to no profit from using them. First feature was
discarded (working site id) as well as some of 24h series. Numbering whole series from 0, series: 0, 11, 13,14, 15, 16, 17, 18, 19, 20, 21 were not used for training. Features containing a-d expert assessments were substituted by numerical values 0-3. Every single feature was scaled to fix range -1 to 1.

####Architecture
In this approach some kind of custom-built feed-forward NN was used to solve problem. First 12 features (work-site id is cut off) are fully-connected to number of hidden units. All of 11 remained series of 24h data readings are stacked on each other to produce some kind of 2D image. On that image convolution windows of different size are applied (11x1, 11x3, 11x6, 11x12 and 11x24). Each window creates many feature maps. Result of this convolution is flattened back to 1D and concatenated with result of mentioned hidden units connected to first 12 features. Next layer is simple fully-connected layer. In some variants there is drop-out layer between those two layers, Using of drop-out with keeping ratio of 0.5 improved stability of learning process but also produced worse results on preliminary tests. Last layer is 2 way softmax. All non softmax neurons use tanh activation function.

####Training
During training cross-entropy error is minimized. AdamOptimizer or RMSPropOptimizer which are bundled with Tensorflow were used as minimizers. Batches of moderate size was balanced: same number of warning and normal labels and shuffled. Due to imbalance of training set, warning examples was used much more frequently what could be the reason of over-fitting on this class. Examples was not randomly sampled from training data but picked sequentially from shuffled dataset to avoid situation in which some of warning examples were not sampled at all. Training performed on 24 thread 200GB memory machine takes less than hour. Best preliminary score of this solution was 0.932.

##Ensemble SVM

####Preparing data
Choosing sub-list of training data was based on assumption the most valuable information emerge from boundary hours of occurrence of “warning” hazard. Training set was prepared by getting all vectors of “warning” class and choosing sequence of measurements a few hours before and after occurrence of “normal”. Based on that data SVM classifier had been tuned following grid search procedure, after tuning classifier was used to score feature vector in backward elimination procedure.

####Choosing of SVMs
First SVM of ensemble was SVM fit in preparing data. Since high value of C allow SVM to select more vectors as support vectors which finally could lead classifier to over-fit second SVM with lower C parameter value was added to ensemble. High value of gamma parameter could prevent SVM from finding boundaries which allow generalize „shape” of area covered by class, based on that fact another SVM fulfilling constraints of low gamma value was selected in procedure of grid search.

####Ensemble SVMs
Finally all SVMs was trained only on 30% of prepared data set and their output was processed by Gaussian Mixture Model to recognize relation between errors of each of them. Tuning mixture of Gaussian shown that best results was reached with only one Gaussian component for each class (which means that instead of GMM Gausian Discriminant Analysis was best choice). Note that for getting information about errors of SVMs on different working sites to input of GMM that information was added. Unfortunately id of working site doesn't be represented by natural distribution, because of that instead of id height of working site was added to input vectors of GMM.
To avoid under-fitting of SVMs procedure of learning model was repeated 200 times (note that SVMs was learnt on set with only around 1000 elements) and based on cross-validation the best classifier was obtained.

##Overall results for our solutions:

 1. SVM, score: 0.9336296, place: 4
 2. Neural Networks, score:	0.91279228, place: 26
 3. Genetic Algorithm, score: 0.90544115, place: 28
