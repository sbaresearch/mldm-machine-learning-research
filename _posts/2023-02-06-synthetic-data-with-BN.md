---
layout: post
title: Increasing privacy for synthetic micro-data with Bayesian Networks
comments: true
---
# Introduction
The use of synthetic data as a privacy-preserving measure for micro-data is gaining more and more popularity, especially due to its ability to maintain data utility. Synthetic data is artificially generated by a model that has been trained on real data. This means that the observations in the synthetic data do not directly correspond to any individual in the original dataset. While there are many tools for creating synthetic data available, only little research has focused on selecting sensitive features and generating synthetic data in a way that concentrates on protecting these selected attributes from inference attacks, while keeping the data utility as high as possible. This can be done by setting certain constraints when learning the model from the original data. The python package DataSynthesizer uses Bayesian Networks that were modified to achieve this goal.

# Problem Statement
Synthetic data tries to reduce the risk of identity and attribute disclosure. Identity disclosure happens when an individual can be identified based on their attribute values. Attribute disclosure, on the other hand, describes the value of a sensitive attribute being discovered. We call Quasi-Identifiers a set of features that cannot uniquely identify an individual themselves but can become a unique identifier when combined. This set of Quasi-Identifiers can then form an attribute key, which can be used to disclose a subject’s identity or sensitive attribute value by learning a model from the data and using the attribute key to predict the sensitive value. While identity disclosure might not be an issue for synthetic data, the risk of attribute disclosure remains. When using common approaches, all variables are treated the same when creating synthetic data. With the method shown, sensitive attributes can be selected and will then be separated from the Quasi-Identifiers and non-sensitive attributes to make the synthetic data less correlated. Meanwhile, the correlation between the target attribute and the rest of the features is preserved. This will then ensure that the data utility does not suffer too much from synthetization, while reducing the risk of attribute disclosure for sensitive values.

# Approach
While this approach has been implemented for one sensitive feature, the implementation for an arbitrary number of sensitive features was done during the course of this project. Subsequently, the influence of the number of sensitive attributes on data utility and privacy was measured. As a first step of data synthetization, the original data is described by a Bayesian network. This is done by building a network that learns correlations between attributes as well as other data properties. The original DataSynthesizer uses a greedy algorithm called GreedyBayes. However, the algorithm used is too slow for datasets with higher dimensions. To speed up the process of building the network, a custom genetic algorithm has been introduced for learning the network. For this project, two scenarios were implemented: For Scenario 1, sensitive features can only have the target variable as their parent. The second scenario allows for sensitive features to have other sensitive features, the target variable or both as their parents. The basic structures of these two scenarios can be seen in the figure below.

![](/assets/images/synthetic-data-with-BN/network_structures.png)

# Experiment Setup
Each dataset was randomly split into training and test data, after that, the training data was synthesized. The five machine learning algorithms k-Nearest-Neighbors, Random Forest, Logistic Regression, Naive Bayes and Support Vector Machine were then applied. To measure the effect of the number of sensitive attributes on the utility, one, two or three features were set as such sensitive features for each dataset. These three sensitive attributes were selected based on feature importance and sensitivity of the variable. To measure the effect of the different settings on the data utility, the mean accuracy of ten random splits was computed for each dataset-algorithm combination, with k = {1, 2, 3, 4}, where k is the maximum number of parents per node. Furthermore, the different outcomes of the two scenarios (with and without sensitive attribute interaction) were evaluated. The utility loss was then estimated by calculating and comparing the accuracies for the original and synthetic datasets. The disclosure risk was assessed by setting the sensitive attributes as target variables and the Quasi-Identifiers as input features (attribute key). Then the same machine learning algorithms as listed above were used to predict the sensitive attributes. In addition, the Generalized Correct Attribution Probability (GCAP) was computed.

# Results
The experiments showed that the newly introduced approach could lead to utility loss, especially for data with a small number of input features. A network that allows interaction between sensitive attributes will generate data with higher utility than a network that does not allow sensitive attribute interaction. This means that if it can be assumed that an adversary does not have information about any of the sensitive features, it is advised to opt for a network built under the conditions of Scenario 2. Using networks with a high value for k and a higher sensitive features will cause almost no utility loss, sometimes even score better than the original data. Networks with k = 1, on the other hand, performed quite poorly. The risk for attribute disclosure on sensitive data variables can be reduced even more by using the introduced approach instead of conventional synthetization methods. Assuming an adversary knew a subject’s values for all Quasi-Identifiers, the probability of their predictions for the sensitive attributes being correct is therefore much higher for the original and the standard synthetic data. Furthermore, the experiments showed that even when adding more than one sensitive feature, the method still produces the desired outcome. However, the introduced approach’s effectiveness is assumed to also depend on the sensitive attributes chosen and their initial correlations with Quasi-Identifiers.

Author: Nina Niederhametner
Edit: Tanja Sarcevic