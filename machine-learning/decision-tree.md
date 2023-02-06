# Decision Tree

## Entropy

Entropy is an information theory metric that measures the impurity or uncertainty in a group of observations. It determines how a decision tree chooses to split data. The image below gives a better description of the purity of a set.

![](https://www.section.io/engineering-education/entropy-information-gain-machine-learning/purity.png)

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## Information Gain

We want to determine which attribute in a given set of training feature vectors is most useful for discriminating between the classes to be learned. Information gain tells us how important a given attribute of the feature vectors is.

<figure><img src="../.gitbook/assets/Screenshot 2023-02-06 at 2.51.16 PM.png" alt=""><figcaption></figcaption></figure>

### ID3

The **steps in ID3 algorithm** are as follows:

1. Calculate entropy for dataset.
2. For each attribute/feature.\
   2.1. Calculate entropy for all its categorical values.\
   2.2. Calculate information gain for the feature.
3. Find the feature with maximum information gain.
4. Repeat it until we get the desired tree.

Characteristics of ID3 Algorithm are as follows:

1. ID3 uses a greedy approach that's why it does not guarantee an optimal solution; it can get stuck in local optimums.
2. ID3 can overfit to the training data (to avoid overfitting, smaller decision trees should be preferred over larger ones).
3. This algorithm usually produces small trees, but it does not always produce the smallest possible tree.
4. ID3 is harder to use on continuous data (if the values of any given attribute is continuous, then there are many more places to split the data on this attribute, and searching for the best value to split by can be time consuming).

**Drawback Of Information Gain**

Information gain is biased towards test with many occurances. Consider a feature that uniquely identifies each instance of a Training set and if we split on this feature, it would result in many brances with each branch containing instances of a single class alone(in other words pure) since we get maximum information gain and hence results in the Tree to overfit the Training set.
