# Kaggle Competition: Covid-19 Global Forecasting (Week 2)
<b> https://www.kaggle.com/c/covid19-global-forecasting-week-2 </b>

This repository provides an example in developing a Mulitvariate Regression Model for Data Prediction.


### Framing the Problem: 

The following challenge is a supervised learning task with labelled training data given to predict the number of confirmed cases and fatalities of Corona patients between 1st April 2020 and 30th April 2020. Given the data and objective, the task required the implementation of a Multivariate Regression Model. <br>

For the evaluation of the regression model, the Root Mean Square Error (RMSE) rate will be used. It indicates the amount of error the system typically makes in its prediction. While RMSE puts higher weight onto larger errors, the Root Mean Squared Log Error (RMSLE) will also be applied for comparison. It is a more robust performance measure when dealing with outliers and provides a relative error between predicted and actual error. Since RMSLE generates larger penalty for underestimation of correct variables than overestimation, it appears to be suitable for forecasting Covid-19 cases as the overestimation of the target variable would be more tolerable and less fatal in future predictions. <br><br>

### Data Exploration and Preparation

With an interest in exploring the impact of demographics on the transmission of Covid-19, I have integrated the population by country dataset (2020) by tanuprabhu which is freely available on Kaggle (see https://www.kaggle.com/tanuprabhu/population-by-country-2020).The population data provides the population of each country with the
 * yearly change, 
 * net change, 
 * density (p/km²), 
 * land area (km²), 
 * net value of migrants, 
 * fertility rate, 
 * median population age, 
 * the percentage of urban population, and
 * the population’s world share contribution. <br>

Before merging the datasets, column and country names have been adapted in both datasets. <br>
The Diamond Princess cruise has been listed as a country within the training and testing data. Population data for Diamond Princess have been manually added whereby essential information could be obtained through online resources. For the preparation of the population data, null values have been either removed or have been replaced by the mode values. The datasets have been merged on country names. <br>
With a partial intersection of dates between training and test data, the date values have been adapted for the training data to end on 18th March 2020 and for the test data to start on 19th March 2020. As the timestamp values cannot be interpreted within the Regression model, new independent values have been created providing the year, month, week, day of the year and day of the week. The official date value has been turned into an integer value. <br>
With a high number of undefined values for the State values, these have been removed and a new variable ‘geo‘ has been generated to combine the country and state names if available. All the data preparation has been applied to both the test and train set. <br>
For the preparation of the input data, correlations between the attributes and the number of confirmed cases and fatalities have been assessed. Both the number of confirmed cases and fatalities have only small correlations with to the size of population (r_cases = 0.15; r_fatalities = 0.12), world share contribution (r_cases = 0.15; r_fatalities =0.12) and net change rate of the population (r_cases = 0.12; r_fatalities = 0.10).  The variables ‘Density’ and ‘dayofweek‘ have been removed showing the lowest correlation with the target values. <br>
The categorical variables country names and geo have been encoded for interpretation. The data has been scaled handle the disparities in units by translating each feature individually such that it is given a range between 0 and 1. The training set has been split to obtain a test and validation set to evaluate models’ performances. <br><br>

### Model Testing
* The <b> Decision Tree Regressor </b> builds regression models in the form of a tree structure. It divides the dataset into smaller subsets while an associated decision tree is being incrementally developed. The result of a decision tree provides decision nodes (predictors tested in branches) and leaf nodes (target value). The tree is used to fit a sine curve whereby the models learn linear regressions approximating the sine curve.
* <b> Random Forest Regression </b> is based on a supervised learning algorithm which applies ensemble learning methods for regression analysis. By constructing a multitude of decision trees based on random samples throughout training, it outputs the mean prediction of the individual trees. It is described as a meta-estimator which combines the result of multiple predictions to improve the predictive accuracy and to control over-fitting. The trees are generated and run in parallel. No interaction occurs between the trees when generated
* <b> Linear Regression </b> is among the simplest regression models. It predicts a dependent variable (output) based on given independent variables (input) and identifies the linear relationship between input and output. 
* A <b> multi-output neural network regression</b> refers to the process of generating more than one output value for a given input data. This regression can be easily generated with Keras neural networks API. The functional model contains one input layer, two Dense layers with ReLU activations and Adam optimizer.  The concatenate layer connects the input and main output of the second hidden layer while the auxiliary output is directly connected to the second hidden layer. There are several types of neural networks, in this case, a feed-forward or perception neural network relays data directly from the front to the back. For each training instance, the backpropagation algorithm starts with a prediction (forward pass) and measures the error, then returns through each layer in reverse order to measure the error contribution from each connection (reverse pass). Ultimately the model tweaks the weights of the connections to reduce the error rate (Gradient Descent step). <br><br>

### Model Selection and Fine-tuning
Comparing the RMSE scores of the typical prediction error of the number of individuals. The <b>Random Forest Regressor</b> appears to have the lowest RMSE score of approximately 68 falsely identified cases of fatality and/or confirmed cases. However, having a much higher MSE score than the Neural Network could suggest overfitting of the model. Nevertheless, due to its promising RMSE and RMSLE scores, the model has been selected for further exploration and tuning.<br>

Using Scikit-Learn’s <b>Randomized Search CrossValidation</b> method, a grid of hyperparameter ranges can be defined. Random samples from the grid are generated performing K-Fold cross-validation with each combination of values which can then identify the best possible hyperparameters for our model.<br><br>

### Conclusion
Overall, my Random Forest Regressor Model provided an RMSLE score of 1.42540, suggesting that the standard deviation is around e^1.4 or within 3.875 times higher or smaller than the true value.  Having a much lower score in the training set indicates an overfitting tendency which can also be observed in the MSE score. Large MSE score indicates the detection of patterns that the model found in the training data which were not existing in the test set. In this case, the neural network model shows less tendency for overfitting and could be further explored. 