---
layout: post
title: Catboosterization

---
***
![Image test]({{ site.url }}/images/Nyan.jpeg)
## Overview
This project uses data from Kaggle's Amazon.com - Employee Access Challenge and applies the Catboost algorithm to predict employee resource access based on role attributes. These role attributes are all categorical. Chartboost is a boosted tree algorithm similar to XGBoost, designed to work well with largely categorical data.

## Motivation
Categorical data is not rare. Everyday we make decision based on categorical heuristics. An example is food, yellow bananas are good ... brown not so much. However, generally machine learning models perform better with numeric values and so categorical data is often ignored. Having Catboost in our toolbox expands the data we can work with and ultimately expands what can be accomplished through Data Science.


## Why not XGBoost
XGBoost, as it is implemented in sklearn, automatically one hot encoding categorical data. This a problem when a feature contains thousands of unique categories. One hot encoding takes each unique categories (minus one) and turns it into a feature with a binary value of zero or one. Large number of features can generally lead to a problem knowns as the [curse of dimensionality](https://en.wikipedia.org/wiki/Curse_of_dimensionality). Specifically tree based algorithms need select features to be roots and some branches. It can go through every possible combination, which prohibitivly expensive. Or it takes a random subset, which in this case unlikely select anything of value.

![Image test]({{ site.url }}/images/Unique_cat.png)


## Enter Catboost
[Catboost](https://catboost.ai) was developed and open sourced by Yandex. Instead of one-hot encoding, it encodes with the formula (countInclass+prior)/(total count+1).

> CountInclass = a cumulative count of assigned class for a combination of features. In my case, the classes are approved or rejected. CounInclass= the number of times this combination was approved or rejected. Total count= count for given category. Prior is value assigned based parameters for the model and i think exists to avoid zero values. There is more involved and don't find the documentation to be very clear,but if you wish learn more, here is a [link] (https://catboost.ai/docs/concepts/algorithm-main-stages_cat-to-numberic.html)


### Evaluation
The data set is imbalanced with approvals to rejections at 16 to 1. Accuracy would be a poor evaluation metric (predicting approval everytime score 94%) and since the Kaggle competition used Roc-Auc score, I will as well. 
#### Roc-Auc Score
> In place of auccary, the metrics of precision and recall are used.  They measure false positives and false negative respectively (TP/(TP+FP), TP/(TP+FN) ). There is a natural tradeoff between these metrics. If you want minimize false positive, assign everything to the negative class and vice versa. ROC curve(receiver operating characteristic curve) plots this trade off. Then compute the AUC (area under curve) and you have the Roc-Auc Score, which measures the overall model performance across precision and recall.

The best submissions scored around .92 Roc-Auc score. If the model always predicted approve, the Roc-Auc would be .85. To evaluate Catboost, a score around .92 would be very good and .85 would poor.



## Performance
Right out of the box, Catboost had ROC AUC score of around .89.  I know we can do better. Yet performing a grid search did not produce higher scores. Note this grid search was not exhaustive. Catboost,  has a lot of machinery under the hood that will select for optimal parameters. A full grid search across all parameters would likely be redundant and I believe why I couldn't break the .89 threshold. However, I noticed diversity in the confusion matrices. This indicated the models were producing different results despite similar scores. This indicated potential for assembling.

>Ensembling basically means combining models together. Models with significant diversity tend to produce better results, the intuition being variances and biases of individuals models are reduced.  Models can be assembled via a voting system, max voting, average voting and weighted voting.  Another method is stacking, which takes the output of one model feeds into another. Boosted models are already stacked. Catboost's algorithm will continuously add trees until model performance stops improving, so I don't think there is much to gain with additional stacking. Assembling via voting is the path forward here.

![Image test]({{ site.url }}/images/ensemble.png)
I selected three models with similar overall Roc-Auc score but variances in accuracy and precision. I ensembled them using max voting. The overall improvement was only .002, but kaggle competition are won by building  very large ensembles to gain small incremental score improvements. Thus, I suspect ensembling more models would be an effective path forward to improve score. The hard will finding models with sufficient diversity.




## Future Work
* Build a grid search for model diversity as well as high scores threshold for better for ensembling
* Try tree based anomaly detection algorithm like Isolation Forest. The class imbalance makes this task an anomaly detection one. However prebuilt implementations of Isolation Forest use one hot encoding. A custom built implementation with Catboost's encoding might be very effective
