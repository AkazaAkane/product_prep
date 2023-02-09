# Ensemble

**Ensemble learning** is a general meta approach to machine learning that seeks better predictive performance by combining the predictions from multiple models.The three main classes of ensemble learning methods are **bagging**, **stacking**, and **boosting.**

* Bagging involves fitting many decision trees on different samples of the same dataset and averaging the predictions.
* Stacking involves fitting many different models types on the same data and using another model to learn how to best combine the predictions.
* Boosting involves adding ensemble members sequentially that correct the predictions made by prior models and outputs a weighted average of the predictions.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

## Random Forest

random forest algorithm:

Step 1: In Random forest n number of random records are taken from the data set having k number of records.

Step 2: Individual decision trees are constructed for each sample.

Step 3: Each decision tree will generate an output.

Step 4: Final output is considered based on _Majority Voting or Averaging_ for Classification and regression respectively.

### Pros <a href="#f1a1" id="f1a1"></a>

* Random Forests can be used for both classification and regression tasks.
* Random Forests work well with both categorical and numerical data. No scaling or transformation of variables is usually necessary.
* Random Forests implicitly perform feature selection and generate uncorrelated decision trees. It does this by choosing a random set of features to build each decision tree. This also makes it a great model when you have to work with a high number of features in the data.
* Random Forests are not influenced by outliers to a fair degree. It does this by binning the variables.
* Random Forests can handle linear and non-linear relationships well.
* Random Forests generally provide high accuracy and balance the bias-variance trade-off well. Since the model’s principle is to average the results across the multiple decision trees it builds, it averages the variance as well.

### Cons <a href="#63f9" id="63f9"></a>

* Random Forests are not easily interpretable as linear regression or decision tree
* Random Forests can be computationally intensive for large datasets.
* Random forest is like a black box algorithm, you have very little control over what the model does.

## Boosting

## Adaboost

AdaBoost is not a model but is a method applied to any classifier giving it the ability to learn its errors and hence suggest an accurate and better model for future use as an AdaBoost classifier example.

In each iteration, AdaBoost identifies miss-classified data points, increasing their weights (and decrease the weights of correct points, in a sense) so that the next classifier will pay extra attention to get them right.

> After training a classifier at any level, ada-boost assigns weight to each training item. Misclassified item is assigned higher weight so that it appears in the training subset of next classifier with higher probability. After each classifier is trained, the weight is assigned to the classifier as well based on accuracy. The more accurate classifier is assigned higher weight so that it will have more impact in the final outcome. A classifier with 50% accuracy is given a weight of zero, and a classifier with less than 50% accuracy is given negative weight.

### **Pros:** <a href="#cbb8" id="cbb8"></a>

* Computational scalability
* Handles missing values
* Robust to outliers

### **Con’s:** <a href="#d71c" id="d71c"></a>

* High Variance
* Inability to extract a linear combination of features.
* Adaboost needs quality data for training as it is very sensitive to noisy data and outliers.

### Gradient Boosting

Just like AdaBoost, Gradient Boosting works by sequentially adding predictors to an ensemble, each one correcting its predecessor. However, instead of tweaking the instance weights at every iteration like AdaBoost does, this method tries to fit the new predictor to the residual errors made by the previous predictor.

First, let’s fit a DecisionTreeRegressor to the training set. Next, we’ll train a second DecisionTreeRegressor on the residual errors made by the first predictor:

```
    from sklearn.tree import DecisionTreeRegressor                   
    tree_reg1 = DecisionTreeRegressor(max_depth=2)          
    tree_reg1.fit(X, y)
    
    y2 = y - tree_reg1.predict(X)
    tree_reg2 = DecisionTreeRegressor(max_depth=2)
    tree_reg2.fit(X, y2)
```

Then we train a third regressor on the residual errors made by the second predictor:

```
    y3 = y2 - tree_reg2.predict(X)
    tree_reg3 = DecisionTreeRegressor(max_depth=2)
    tree_reg3.fit(X, y3)
```

Now we have an ensemble containing three trees. It can make predictions on a new instance simply by adding up the predictions of all the trees:

`y_pred = sum(tree.predict(X_new) for tree in (tree_reg1, tree_reg2, tree_reg3))`

Advantages of Gradient Boosting are:

* Often provides predictive accuracy that cannot be trumped.
* Lots of flexibility - can optimize on different loss functions and provides several hyper parameter tuning options that make the function fit very flexible.
* No data pre-processing required - often works great with categorical and numerical values as is.
* Handles missing data - imputation not required.

Pretty awesome, right? Let us look at some disadvantages too.

* Gradient Boosting Models will continue improving to minimize all errors. This can overemphasize outliers and cause overfitting.
* Computationally expensive - often require many trees (>1000) which can be time and memory exhaustive.
* The high flexibility results in many parameters that interact and influence heavily the behavior of the approach (number of iterations, tree depth, regularization parameters, etc.). This requires a large grid search during tuning.

### XGBoost&#x20;

stands for e**X**treme **G**radient **B**oosting

**LightGBM** is a gradient boosting framework based on decision trees to increases the efficiency of the model and reduces memory usage. It uses two novel techniques: **Gradient-based One Side Sampling** and **Exclusive Feature Bundling (EFB).**&#x20;

**LightGBM** splits the tree leaf-wise as opposed to other boosting algorithms that grow tree level-wise. It chooses the leaf with maximum delta loss to grow. Since the leaf is fixed, the leaf-wise algorithm has lower loss compared to the level-wise algorithm. Leaf-wise tree growth might increase the complexity of the model and may lead to overfitting in small datasets.
