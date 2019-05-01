---
layout: post
title: Catboosterization

---
***
![Image test]({{ site.url }}/images/Nyan.jpeg)
## Overview
This project uses data from Kaggle’s Amazon.com - Employee Access Challenge and applies the Catboost algorithm to predict whether an employee will be granted access to a particular resource based on certain attributes of the employee’s role, such as manager’s id and department. These role attributes are all categorical. Catboost is a boosted tree algorithm similar to XGBoost, but designed to work well with largely categorical data.

## Motivation
Generally, machine learning models perform better with purely quantitative inputs/outputs, and large amounts of categorical data is often ignored. However, categorical data is common and important for many processes and decisions (e.g. yellow bananas are good… brown not so much).  

Having algorithms that handle categorical data, such as XGBoost and Catboost, in the machine learning toolbox expands the data we can work with and ultimately expands what can be accomplished through Data Science.



## Why not XGBoost
XGBoost, as it is implemented in sklearn, automatically “one hot encodes” categorical data. One hot encoding takes each unique category (minus one) and turns it into a feature with a binary value.This is a problem when a features contains thousands of unique categories. An example is the feature RoleID, that contains thousand of different role categories.A large number of features can leads to a problem known as the curse of dimensionality; as dimensions, or features, increase data points become more distant , making it harder to discover relationships because everything is far away. Specifically tree-based algorithms must select some features to be roots and some branches. To make this determination, they can go through every possible combination, which is prohibitively expensive. Or they can take a random subset, which is  unlikely to select anything of value since most features have been encoded into  dummy variables of zero or one.


![Image test]({{ site.url }}/images/Unique_cat.png)


## Enter Catboost
[Catboost](https://catboost.ai) was developed and open-sourced by Yandex. Instead of one-hot encoding, it encodes with the formula (countInclass+prior)/(total count+1).

> CountInclass = a cumulative count of assigned class for a combination of features. In my case, the classes are approved or rejected. CounInclass= the number of times this combination was approved or rejected. 
> Total count = the cumulative count for the given category. 
>Prior= a value assigned based on initial parameters for the model which exists to avoid zero values. 

More information here:[link] (https://catboost.ai/docs/concepts/algorithm-main-stages_cat-to-numberic.html) 


### Evaluation
The data set is imbalanced with approvals to rejections at 16 to 1. Accuracy would be a poor evaluation metric, since a model that predicted approval every time would score 94% accuracy. The Roc-Auc score is a good alternative. 
 
#### Roc-Auc Score
> In place of accuracy, the Roc-Auc score incorporates metrics of Precision and Recall to evaluate a model. High Precision indicates low occurrence of false positives;  high Recall indicates low occurrence of false negatives. There is a natural trade-off between these metrics. The occurrence of false positives vs. false negatives can be adjusted by lowering or raising a decision threshold. The ROC curve (receiver operating characteristic curve) plots Recall vs. 1-Precision. The point (1,1) represents a threshold with perfect Recall, no false negatives, but max false positives. Computing the AUC (area under curve) produces the Roc-Auc Score, which measures the overall model performance for precision and recall, across all possible decision thresholds.


The best submissions scored around .92 Roc-Auc score. If the model always predicted approve, the Roc-Auc would be .85. To evaluate Catboost, a score around .92 would be very good and .85 would be poor.



## Performance
When I first applied Catboost to my data, the model had a ROC-AUC score of 0.89. I attempted to increase it by performing a grid search, a loop through the model’s hyperparameters, but this did not produce higher scores. However, it did produce notable differences in the confusion matrices. This indicated the models were producing different results despite similar scores, suggesting the potential for ensembling.


>Ensembling is a process of combining models together. Models with significant diversity tend to produce better results, likely because variances and biases of individual models are balanced against each other. Models can be assembled via stacking or a voting system. Stacking takes the output of one model and feeds into another. Boosted models are already stacked: Catboost’s algorithm will continuously add trees until model performance stops improving, so I don’t think there is much to gain with additional stacking. 

I decided to use voting system to strengthen the model.
![Image test]({{ site.url }}/images/ensemble.png)
I selected three models with similar overall Roc-Auc scores which had variances in accuracy and precision. I ensembled them using max voting or majority voting. The overall improvement was 0.002. Kaggle competitions are often won by building very large ensembles to gain small incremental score improvements. Thus, I suspect ensembling more models would be an effective path forward to improve score, provided I can find models with sufficient diversity.




## Future Work
* Build a grid search for model diversity within high score threshold for better for ensembling
* Try tree-based anomaly detection algorithm like Isolation Forest. The class imbalance makes this task an anomaly detection one. However, prebuilt implementations of Isolation Forest use one hot encoding. A custom built implementation with Catboost’s encoding might be very effective