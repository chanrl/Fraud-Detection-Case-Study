# Fraud-Detection-Case-Study

## Premise

You are a contract data scientist/consultant hired by a new e-commerce site to try to weed out fraudsters. The company unfortunately does not have much data science expertise... so you must properly scope and present your solution to the manager before you embark on your analysis. Also, you will need to build a sustainable software project that you can hand off to the companies engineers by deploying your model in the cloud. Since others will potentially use/extend your code you NEED to properly encapsulate your code and leave plenty of comments.

## Overview

This was a collaborative case study with [Sean Findley-McMillon](https://github.com/sgmcmillon) and [Ethan Lemus](https://github.com/poxlox). Our objective was to build a Flask web app dealing with e-commerce transactions that could pull in new data regularly, identify new events that are worthy of investigation for fraud, and update a display of the investigated data.

## Process Flow

First we identified features that had value in modeling then preprocessed those features to prepare a dataset that can be trained on by our models. A wide array of models (Gradient Boosted, Random Forest, Neural Network, etc.) were used with varying hyperparameters and the presence of SMOTE. These models were validated using a train test split of 20% on their ROC AUC scores.

#### Preprocessing

The target of our model are the account types containing the word 'fraud' in them. We created a boolean column that indicated if that row was fraudulent then discarded the account type column. 

Features were broken into their respective anticipated types (Boolean, Categorical, Date, and Continuous) and converted to those types. The NaN values found in the continuous and date variables were filled with the median of thier respective columns, and a separate column was created to indicate whenever a row had NaN values that were replaced.

The date columns were converted from Unix timestamps to datetime objects. We extracted the hour and the day of week into new columns then discarded the rest.

Regarding the ticket_types column, we were able to extract quantities sold and total # of previous payouts from the values in that column.

There were 8 features that we deemed had no relevant information that we could easily access so we discarded them.

There were 3 features that we felt had no internal relevance other than their presence. We discarded these columns and replaced them with booleans if they previously existed or not.

#### Accuracy Metrics

ROC AUC (Area Under the Receiver Operating Characteristic Curve) was chosen as our evaluation metric as in this case study, since accuracy score does not reflect well on our model score as the data is imbalanced with only a minority of the data classified as fraud. The model can still return a high accuracy score even if it only predicts false for every single item.

Thus, we are more interested in both the false positive rate (precision) and true positive rate (recall), which is simulataneously captured in the AUC score versus only recall and precision scores.

#### Validation and Testing

We held out 20% of the dataset for validation. Validation was conducted by comparing our model's predictions of the test data subset to the actual test data results.

#### Parameter Tuning

Using GridSearchCV from sklearn, we input a range of values on a couple of parameters that are commonly hypertuned for random forest and gradient boosted models. We then choose the best parameters chosen by GridSearchCV.

#### Further Steps

There were some features we could further extract such as cost in the 'ticket_types' column, and the text columns can be explored and processed with NLP to discover more features that might be predictive for fraud.

## Results

Our best model (Gradient Boosted model that had SMOTE applied to the training data) was able to net an accuracy of 98.7%, with a precision score of 95.4%, and recall score at 91.2% at the standard threshold of 50%. 

A Flask app was built and deployed on an AWS EC2 instance that was able to display live transactions with their probability of fraud. After the model made the predictions on the transactions, it was then stored on a MongoDB on the same instance.
