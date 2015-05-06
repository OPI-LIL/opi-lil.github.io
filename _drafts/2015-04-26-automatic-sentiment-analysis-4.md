---
layout: post
title: "Automating sentiment analysis 4"
description: "This is final post in four post series about Automating Sentiment Analysis. In this part we discuss machine learning algorithms used to analyze text emotions."
category: [Sentiment Analysis]
tags: [Java, Machine Learning, Sentiment Analysis]
---

This is final post in four post series about Automating Sentiment Analysis. In this part we discuss machine learning algorithms used to analyze text emotions.

<!--more-->

Having retrieved Sentiment Vectors for 30101 text that formed training dataset (see part 3 of series), of which 14053 were tagged as positive and 16048 as negative, we assessed 5 machine learning algorithms: NEAT and FeedForward Neural Networks, Support Vector Machines, Deep Belief Networks and Naive Bayes. 
####Algorithm implementation
#####NEAT, FeedForward Neural Networks and Support Vector Machines
NEAT, FeedForward Neural Networks and Support Vector Machines were implemented using Encog library. Using this library allowed us to easily implement those algorithms and switch between them in easy fashion, with added benefit of using tested and stable code, thus eliminating possible implementation induced bugs.

Below is part of the code responsible for 10-fold cross-validation for these algorithms:

```java
VersatileDataSource source = new CSVDataSource(dataFile, false,   CSVFormat.DECIMAL_POINT);
VersatileMLDataSet data = new VersatileMLDataSet(source);
data.defineSourceColumn("in-emotion", 0, ColumnType.continuous);
data.defineSourceColumn("in-length", 1, ColumnType.continuous);
data.defineSourceColumn("in-strongest-positive", 3, ColumnType.continuous);
data.defineSourceColumn("in-strongest-negative", 4, ColumnType.continuous);
data.defineSourceColumn("in-positive-hits", 5, ColumnType.continuous);
data.defineSourceColumn("in-negative-hits", 6, ColumnType.continuous);

ColumnDefinition outputColumn = data.defineSourceColumn("emotion", 8, ColumnType.nominal);

data.analyze();

data.defineSingleOutputOthersInput(outputColumn);

EncogModel model = new EncogModel(data);
if (programOptions.getModel().equals("svm"))
	model.selectMethod(data, MLMethodFactory.TYPE_SVM);
if (programOptions.getModel().equals("neat"))
	model.selectMethod(data, MLMethodFactory.TYPE_NEAT);
if (programOptions.getModel().equals("ff"))
	model.selectMethod(data, MLMethodFactory.TYPE_FEEDFORWARD);
if (programOptions.isTrain()) {
	model.setReport(new ConsoleStatusReportable());
}
data.normalize();

model.holdBackValidation(0.1, true, 1001);
model.selectTrainingType(data);
machineLearningMethod = (MLRegression) model.crossvalidate(10, true);

System.out.println(String.format("done"));
System.out.println("\tTraining error: " + 
    EncogUtility.calculateRegressionError(machineLearningMethod, model.getTrainingDataset()));
System.out.println("\tValidation error: " + 
    EncogUtility.calculateRegressionError(machineLearningMethod, model.getValidationDataset()));
```

#####Deep Belief Network
Deep Belief Network was implemented using Deeplearning4J. Code for evaluation of DBN is shown below

```java
 DataSet completedData = new DataSet(data, Nd4j.create(outcomes));
completedData.shuffle();
completedData.normalizeZeroMeanZeroUnitVariance();
SplitTestAndTrain splittedDataset = completedData.splitTestAndTrain(5000);
DataSet teachDataset = splittedDataset.getTrain();
DataSet testDataset = splittedDataset.getTest();
RandomGenerator gen = new MersenneTwister(123);
   
NeuralNetConfiguration conf = new NeuralNetConfiguration.Builder()
	.iterations(100)
	.weightInit(WeightInit.VI)
    .dist(Distributions.normal(gen, 1e-3))
    .constrainGradientToUnitNorm(false)
	.lossFunction(LossFunctions.LossFunction.RECONSTRUCTION_CROSSENTROPY)
    .activationFunction(Activations.tanh())
	.rng(gen)
    .visibleUnit(RBM.VisibleUnit.GAUSSIAN)
    .hiddenUnit(RBM.HiddenUnit.RECTIFIED).dropOut(0.3f)
	.learningRate(1e-3f)
    .nIn(paremetersNum).nOut(2).build();

DBN dbn = new DBN.Builder()
	.configure(conf)
	.hiddenLayerSizes(new int[]{3, 2, 3})
	.build();

NeuralNetConfiguration.setClassifier(dbn.getOutputLayer().conf());

dbn.fit(teachDataset);

Evaluation eval = new Evaluation();
INDArray output = dbn.output(testDataset.getFeatureMatrix());
eval.eval(testDataset.getLabels(), output);

if (programOptions.isTrain()) {
    System.out.println(String.format("Score %s", eval.stats()));
}
```

#####Naive Bayes
Naive Bayes classifier was implemented using exdended library from Philipp Nolte. Code for evaluation of Naive Bayes classifier is shown below

```java
List<Pair<String,String>> validateSet = dataset.subList(validateStart+1, validateEnd-1);
ArrayList<Pair<String,String>> trainingSet = new ArrayList<Pair<String,String>>();
trainingSet.addAll(dataset.subList(0,validateStart));

if (validateEnd >= dataset.size()) validateEnd--;
trainingSet.addAll(dataset.subList(validateEnd,dataset.size()-1));

for (Pair<String,String> lineValue : trainingSet) {
    negation = false;
    List<String> tokens = new ArrayList<String>();
    Sting[] words = lineValue.getKey()
                            .replace(";", " ")
                            .replace("-", " ")
                            .replace(":", " ")
                            .replace("'", "")
                            .replace("\"", "")
                            .split("[\\?\\!\\.\\s\\,]+")
    
    for (String word : words)
        if (getStemmedWord(word) != null) {
            if (word.equals("nie")) negation = true;
            else {
                if (!ignoreWords.containsKey(word)) {
                    if (negation) { tokens.add("nie".concat(getStemmedWord(word))); negation = false; }
                    else tokens.add(getStemmedWord(word));
                }
            }
        }
    bayes.learn(lineValue.getValue(), tokens);
}

for (Pair<String,String> lineValue : validateSet) {
    negation = false;
    List<String> tokens = new ArrayList<String>();
    for (String word : lineValue.getKey().replace(";", " ").replace("-", " ").replace(":", " ").replace("'", "").replace("\"", "").split("[\\?\\!\\.\\s\\,]+"))
        if (getStemmedWord(word) != null) {
            if (word.equals("nie")) negation = true;
            else {
                if (!ignoreWords.containsKey(word)) {
                    if (negation) { tokens.add("nie".concat(getStemmedWord(word))); negation = false; }
                    else tokens.add(getStemmedWord(word));
                }
            }
        }
    if (bayes.classify(tokens).getCategory().equals(lineValue.getValue())) {
        correct++;
    }
}
if (programOptions.isTrain()) {
    System.out.println(String.format("%s/10 : Fold #%s Training size %s",fold+1, fold+1,trainingSet.size()));
    System.out.println(String.format("%s/10 : Fold #%s Validate size %s",fold+1,fold+1,validateSet.size()));
    System.out.println(String.format("%s/10 : Fold #%s Accuracy %s",fold+1,fold+1,correct/validateSet.size()));
}
```

This code also contains part for splitting training dataset for two sets: one for training and one for validation, since library does not containg 10-fold cross-validation validation.

####Results
Below are results for 10-fold cross validation for each tested algorithm. 

| Method      | Accuracy |
|-------------|----------|
| DBM         | 0,599    |
| NEAT        | 0,85     |
| Naïve Bayes | 0,72     |
| FeedForwad  | 0,5      |
| SVM         | 0,81     |

We found that NEAT neural network provided best results, with SVM at second place. Drawback of these two methos is long training time, but this can be saving trained model for future use.