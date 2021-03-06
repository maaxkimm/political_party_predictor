# Machine Learning Autoregressive Logistic Regression Model

# Technologies & Libraries I Used
- Python
- Jupyter Notebook
- Pandas
- Scipy
- Scikit-learn
- GeoPy
- NumPy
- Seaborn
- Plotly

# Hypothesis and Model
- Safety protocols, specifically the vaccinations daily rate per capita and tests daily rate per capita, are positively correlated with whether a given state is Republican, and thus, the extent of the impact of COVID-19, measured by the cases and deaths daily rate per capita, are negatively correlated with whether a given state is Republican.

# Datasets
- I extracted “presidential_elections.csv” data from https://sda.berkeley.edu/sdaweb/analysis/?dataset=gss16, which contained data on how the 50 states plus Washington D.C. voted in presidential elections between 1972 to 2016 and appended the presidential election data from 2020. Additionally, I used the vaccinations data, cases data, and imported two more external datasets: https://github.com/nytimes/covid-19-data for the time-series death data and https://github.com/govex/COVID-19/tree/master/data_tables/testing_data for the time-series total tests done data. I utilized each of the datasets to generate the 5 features mentioned in the hypothesis. Ultimately, I planned to validate or invalidate the hypothesis by reading the model weights and odds ratios and using them to accept or reject the hypothesis based on their sign. I will accept the hypothesis if all 5 model weights and odd ratios align with the hypothesis and reject the hypothesis if at least one of them does not align with the hypothesis.

# Result
- Although I chose to accept the hypothesis based on the signs of the model coefficients and odds ratios from the model, I recognize the limitations of the hypothesis because I chose to leave out the magnitudes of these ratios in assessing the hypothesis since I didn’t hypothesize about the strength of the correlations between the features and the political ties of a state. The model coefficients and odds ratios come from the equation: f(x) = 1 / (1 + e-(.70 - 24.35x1-20.82x2+362.90x3+8.92x4-173.16x5)). After the intercept, from left to right, the order of the features were: people_fully_vaccinated, people_partially_paccinated, daily_case, deaths_diff_per_capita, and the tests_diff_per_capita. After extracting the signs of the coefficients and odds ratios of the model, I concluded that the results align with the hypothesis.

# Feature Selection Plots

<img width="358" alt="Screen Shot 2022-01-11 at 9 36 35 PM" src="https://user-images.githubusercontent.com/68135908/149070875-d84a5fa7-d945-4237-9690-542e66168661.png">
<img width="651" alt="Screen Shot 2022-01-11 at 9 36 06 PM" src="https://user-images.githubusercontent.com/68135908/149070879-b3c58574-629c-41e0-a592-7d0ea75634cf.png">
<img width="684" alt="Screen Shot 2022-01-11 at 9 36 19 PM" src="https://user-images.githubusercontent.com/68135908/149070881-9ef70fe3-1922-47ca-9fef-f87e1c05338e.png">

# Modeling Process
- With the model, I used a logistic regression model with the vaccination per capita daily rate (i.e. difference between each day count divided by POPESTIMATE2020 for both partially and fully), the cases per capita daily rate (i.e. difference between each day divided by POPESTIMATE2020), tests per capita daily rate (i.e. difference between each day divided by POPESTIMATE2020), and the deaths per capita daily rate (i.e. difference between each day divided by POPESTIMATE2020) as the inputs to “predict” whether a given state is Democratic: 0 or Republican: 1 as the output. For context, the last two features were added after the baseline model suffered from low training and validation accuracy; thus, I pivoted the hypothesis to be slightly more specific.

- I chose to use a logistic regression model because I'm predicting discrete values to understand the possible relationship between safety protocols and COVID-19’s impact and whether a given state is Republican. I chose to use the default, cross-entropy loss, as I’ve seen the infeasibility of using L2 loss/MSE for logistic regression models and for my model. I split the mask-usage data into training and test sets. Then, I used the training data when designing the model and used cross-validation to test generalization.

# Performance & Testing
<img width="639" alt="Screen Shot 2022-01-11 at 9 37 06 PM" src="https://user-images.githubusercontent.com/68135908/149070849-333f2e41-906f-49e3-9602-bc1241f91374.png">

- I first measured model performance comparing the ROC curves of the baseline and the improved model.

- I trained the logistic regression model, and the resulting training accuracy was 65%, relatively higher than the baseline logistic regression model which would attain 51%. I created a ROC Curve plot to analyze and compare the predictive performance of the two models. The ROC curve of the baseline model is closer to a constant line of slope 1; it shows that the baseline model is rather a random classifier, with no predictive value. In contrast, the ROC Curve of the improved model is smoother where the curve is closer to the top-left corner. This indicates that it has a better performance compared to the baseline, because the curve shows that the false positive rate is lower and the true positive rate is higher. The precision of the improved model was 0.628, implying that 62.8% of the positive predictions were correct predictions. The recall of the improved model was 0.71, implying that 71% of the positive predictions of all observations were correct. Although it is evident that the improved model performs better than the baseline model, the ROC curve looks less like what the ideal, well-performing model would look like. Thus, the model result isn’t necessarily good, and this motivates a further model improvement in the future.

# Experiments
<img width="768" alt="Screen Shot 2022-01-11 at 9 36 52 PM" src="https://user-images.githubusercontent.com/68135908/149070754-e2abaebb-87aa-4aba-b00d-a4f7d9dcc9ae.png">

- Moving on, I wanted to experiment with the predictive power of my improved model and compare such with the baseline model. In particular, I trained the two models to cumulatively predict the next day upto 2 days and 14 days. As shown in Figure H, AUC score of the improved model is 0.682 (2 days prediction) and 0.686 (14 days prediction), higher than that of baseline (0.533, 0.539 for 2 days and 14 days, respectively). Given that the AUC score measures the ability of the model to distinguish between classes, I could interpret that the improved model correctly labeled between democrats and republicans 68% of the time, while the baseline model was still close to a random guesser. It was interesting to see that the AUC score for the 14 days prediction was slightly higher than that of the 2 days prediction; this could imply that my model is slightly better at long term prediction. 

- Extending on the experiment done, I wanted to try different k-values (1 to 50 days, incrementing by 5) and see how my model would predict larger k-values. Here, I could confirm that the aforementioned insight of my model was correct; my model makes better long term cumulative predictions, even up to 50 days. As shown in Figure J, the AUC score of the improved model increased when I tested 25 - 50 days, while that of the baseline model decreased starting from 30 days and on. This shows that not only my improved model predicts better than baseline model, but also suggests that it makes better long term predictions than baseline. 
After exploring different k-values, I was interested to investigate whether my model was also capable of making explicit predictions instead of cumulative predictions of adjacent days. As shown in Figure I, the AUC score of the improved model for both 5 & 10 days prediction plunged to 52% ~ 56% although it was still slightly higher than that of baseline model. This indicates that my model is incapable of making explicit future predictions when the training data for each of previous days are not fed. However, it is worthwhile noting that the improved model's AUC score for 5 days prediction is conspicuously higher than that of 10 days prediction. This indicates that although my model may be incompetent at making explicit predictions, it still has better short term predictability than long term. Result shown in Figure I particularly motivates us to further improve my model, because I could observe that my model struggles to make explicit predictions, which was contrary to my expectation after experimenting with cumulative predictions. 

# Closing Thoughts
- In the future, I wanted to further explore other possible COVID-19-related features that may be correlated with whether a given state is Republican. Currently, the model includes safety protocols such as vaccination and testing rates, but I wanted to explore the possible relationships between other safety protocols such as mask-usage and the political ties of a given state. However, for the mask-usage for example, I was unable to find time-series data for mask-usage frequency rates and only had the mask-usage data given from part 1 of the project. This data only consists of mask-usage statistics based on a survey conducted from July 2 to July 14 in 2020. If given more time, I wanted to possibly find other time-series data for mask-usage or if unavailable, look for other possible COVID-19 features related to safety protocols to include in the model to better predict the political ties of a given state. This would be interesting to explore, since it may not only improve the model’s overall predictive accuracy, but also improve the ability to make explicit short and long term predictions. Additionally, I would also have liked to include features related to COVID-19’s impact to improve the accuracy of the model. Then, with these features, in the future, I would like to also look more deeply into the level of correlation for each of these features with the political ties of a state. 

- Ultimately, since the experiment aimed to explore the possible relationships between a state’s political ties and what that might mean for the safety of the individuals within that state during a pandemic such as COVID-19, I think there are many ways I could improve our model to help better strongly establish the existence of these relationships.
