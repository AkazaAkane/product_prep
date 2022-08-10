# Imbalanced Data

### 0) Can You build a good model with current data?

If you have an imbalanced data set, first try training on the true distribution**.** If the model works well and generalizes, you're done!&#x20;

### 1) Can You Collect More Data?

A larger dataset might expose a different and perhaps more balanced perspective on the classes. More examples of minor classes may be useful later when we look at resampling your dataset.

### 2) Try Changing Your Performance Metric

I recommend looking at the following performance measures that can give more insight into the accuracy of the model than traditional classification accuracy:

**Confusion Matrix**: A breakdown of predictions into a table showing correct predictions (the diagonal) and the types of incorrect predictions made (what classes incorrect predictions were assigned).

**Precision**: A measure of a classifiers exactness.

**Recall**: A measure of a classifiers completeness

**F1 Score** (or F-score): A weighted average of precision and recall.

**Kappa** (or Cohen’s kappa): Classification accuracy normalized by the imbalance of the classes in the data.

**ROC Curves**: Like precision and recall, accuracy is divided into sensitivity and specificity and models can be chosen based on the balance thresholds of these values.

### 3) Try Resampling Your Dataset

You can change the dataset that you use to build your predictive model to have more balanced data.

You can add copies of instances from the under-represented class called over-sampling (or more formally sampling with replacement), or You can delete instances from the over-represented class, called under-sampling.

_Consider testing under-sampling when you have an a lot data (tens- or hundreds of thousands of instances or more)_

_Consider testing over-sampling when you don’t have a lot of data (tens of thousands of records or less)_

_Consider testing random and non-random (e.g. stratified) sampling schemes._

_Consider testing different resampled ratios (e.g. you don’t have to target a 1:1 ratio in a binary classification problem, try other ratios)_

### 4) Try Generate Synthetic Samples

A simple way to generate synthetic samples is to randomly sample the attributes from instances in the minority class.

You could sample them empirically within your dataset or you could use a method like Naive Bayes that can sample each attribute independently when run in reverse. You will have more and different data, but the non-linear relationships between the attributes may not be preserved.

There are systematic algorithms that you can use to generate synthetic samples. The most popular of such algorithms is called SMOTE or the Synthetic Minority Over-sampling Technique. As its name suggests, SMOTE is an oversampling method. It works by creating synthetic samples from the minor class instead of creating copies. The algorithm selects two or more similar instances (using a distance measure) and perturbing an instance one attribute at a time by a random amount within the difference to the neighboring instances.

### 5) Try Different Algorithms

Decision trees often perform well on imbalanced datasets. The splitting rules that look at the class variable used in the creation of the trees, can force both classes to be addressed. If in doubt, try a few popular decision tree algorithms like C4.5, C5.0, CART, and Random Forest.

### 6) Try Penalized Models

You can use the same algorithms but give them a different perspective on the problem.

&#x20;Penalized classification imposes an additional cost on the model for making classification mistakes on the minority class during training. These penalties can bias the model to pay more attention to the minority class.

&#x20;Often the handling of class penalties or weights are specialized to the learning algorithm. There are penalized versions of algorithms such as penalized-SVM and penalized-LDA.

&#x20;It is also possible to have generic frameworks for penalized models. For example, Weka has a CostSensitiveClassifier that can wrap any classifier and apply a custom penalty matrix for miss classification.

&#x20;Using penalization is desirable if you are locked into a specific algorithm and are unable to resample or you’re getting poor results. It provides yet another way to “balance” the classes. Setting up the penalty matrix can be complex. You will very likely have to try a variety of penalty schemes and see what works best for your problem.

### 7) Try a Different Perspective

There are fields of study dedicated to imbalanced datasets. They have their own algorithms, measures and terminology. Two you might like to consider are anomaly detection and change detection.

**Anomaly detection** is the detection of rare events. This might be a machine malfunction indicated through its vibrations or a malicious activity by a program indicated by it’s sequence of system calls. The events are rare and when compared to normal operation. This shift in thinking considers the minor class as the outliers class which might help you think of new ways to separate and classify samples.

**Change detection** is similar to anomaly detection except rather than looking for an anomaly it is looking for a change or difference. This might be a change in behavior of a user as observed by usage patterns or bank transactions.

### 8) Try Getting Creative

For inspiration, take a look at the very creative answers on Quora in response to the question “In classification, how do you handle an unbalanced training set?”

For example:

Decompose your larger class into smaller number of other classes…

use a One Class Classifier… (e.g. treat like outlier detection)

resampling the unbalanced training set into not one balanced set, but several. Running an ensemble of classifiers on these sets could produce a much better result than one classifier alone

## &#x20;Reference

{% embed url="https://machinelearningmastery.com/tactics-to-combat-imbalanced-classes-in-your-machine-learning-dataset/" %}

{% embed url="https://developers.google.com/machine-learning/data-prep/construct/sampling-splitting/imbalanced-data" %}

