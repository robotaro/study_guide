# Classical Machine Learning

# 1. Features

## 1.1 Feature Selection
* **Wald's test** : Used to detect which features don't contribut to a classification

# 2. Regression

## 2.1 Linear Regression
* Fit a line to the points using least-squares
* Don't forget to calculate the R^2 value to see if that is a good fit
* Don't forget to calculat the p-value to see if that is statistic relevant

# 3. Classification

## 3.1 **Logistic Regression**
* Binary classifier
* It fits an s-shaped logistic function that goes from 0 to 1 showing the **probability** of a data point belonging to each of the classes.
* You calculate the **maximum likelyhood** that will best separate the data.
    1. Pick a probabilty of a data point, scaled but its dimenional values
    2. Calculate the likelyhood of observing data pointw with a given distribution
    3. Shift the distribution and recalculate the likelyhoods
    4. Pick the distribution that best maximimses the likehoods