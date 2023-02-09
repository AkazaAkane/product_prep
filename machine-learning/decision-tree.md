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

### c4.5

C4.5 algorithm is improvement over ID3 algorithm, where “**C**” is shows algorithm is written in C and **4.5** specifics version of algorithm.

<img src="../.gitbook/assets/image.png" alt="" data-size="original">, Intrinsic Information: ![](<../.gitbook/assets/image (8) (1).png>)

C4.5 uses a pruning technique based on statistical confidence estimates. This technique has the advantage that it allows all of the available labeled data to be used for training. C4.5 compares the upper limits of the error confidence intervals for the two trees. For the unpruned tree, the upper error estimate is calculated as a weighted average over its child leaves. Whichever tree has a lower estimated upper limit on the error rate "wins" and is selected.

#### CART

The Gini index is a metric for the classification tasks in CART. It stores the sum of squared probabilities of each class. It computes the degree of probability of a specific variable that is wrongly being classified when chosen randomly and a variation of the Gini coefficient.  The degree of the Gini index varies from 0 to 1. ![](<../.gitbook/assets/image (7) (1).png>)

* Where 0 depicts that all the elements are allied to a certain class, or only one class exists there.
* The Gini index of value 1 signifies that all the elements are randomly distributed across various classes, and
* A value of 0.5 denotes the elements are uniformly distributed into some classes.

CART in classification cases uses Gini Impurity in the process of splitting the dataset into a decision tree. On the other hand CART in regression cases uses least squares, intuitively splits are chosen to minimize the **residual sum of squares** between the observation and the mean in each node.



#### Conclusion

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### Regression Tree



## Pruning

#### **Pre Pruning**(Forward Pruning)

This approach stops the non-significant branches from generating. It terminates the generation of new branch based on the given condition.

min\_sample\_split,min\_sample\_leaf,max\_leaf\_node,max\_features,

#### **Post Pruning**(Backward Pruning):&#x20;

Full tree is generated and then the non-significant branches are pruned/removed. Cross validation is performed at every step to check whether addition of the new branch leads to increase in accuracy. If not the branch is converted to leaf node.

**Cost complexity Pruning/Error-Complexity Pruning**

The complexity parameter is used to define the cost-complexity measure, Rα(T) of a given tree T: **Rα(T)=R(T)+α|T|,** where |T| is the number of terminal nodes in T and R(T) is traditionally defined as the total misclassification rate of the terminal nodes.&#x20;

This method penalize larger trees.

****

