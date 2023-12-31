# Recipes Rating Prediction
by Kristina Wu and Yishan Cai 
<br>

---

## Framing the Problem
### Overview
Recipe ratings on culinary websites like [Food.com](https://www.food.com/recipe/chickpea-and-fresh-tomato-toss-51631) play a crucial role in shaping consumer preferences and culinary trends. High-rated recipes often set benchmarks for quality and taste, influencing what people choose to cook and eat. These ratings, ranging from 1 to 5, offer a more granular view of user satisfaction and preferences. Analyzing the factors that contribute to a recipe's rating is not only beneficial for cooking enthusiasts and chefs but also for the culinary industry as a whole.<br>

The characteristics of a recipe, such as the number of ingredients and calorie amount, can offer deep insights into its popularity and acceptance among users. Different eating style might have varied trends; for instance, some people with health-awareness might favor recipes with diverse ingredients or lower calories. Understanding these preferences is key to predicting the success of a recipe in terms of its rating.<br>

By predicting the rating of a recipe, we can acquire a nuanced understanding of consumer preferences in different culinary segments. This predictive analysis can help identify which recipes are likely to be well-received and why, based on their inherent features. Such insights are invaluable for food bloggers, recipe developers, and culinary websites in tailoring their content to meet user expectations.<br>

To achieve accurate predictions, we will analyze historical data from Food.com, focusing on recipes and their ratings 2008-2018. We especially pay attention on reviews which might have positive correlation to the rate that users post. Going through those reviews, we can figure out some signal words that indicate the positive, negative, or neutral property of users’ opinion on a specific recipe. <br>
This dataset also includes diverse features like the number of ingredients, calorie count, saturated fat, sodium, protein, carbohydrates and so on. By exploring these dimensions, we aim to uncover patterns and correlations that influence a recipe’s rating.<br>

**Notes**:  At the time of prediction, we would have access to the all features mentioned above for recipe rating prediction problem. We would not have any future information beyond what is available in the dataset.<br>

By training a **multi-class classification model** using this data, we aim to develop a predictive tool that can assist in identifying whether a healthier recipe is likely to gain a higher rating, based on the available information at the time of prediction. This can ultimately contribute to a more dynamic and responsive culinary culture. In this way, chefs can refine their recipes to better meet consumer tastes, and the culinary industry can gain valuable insights into emerging trends and preferences.<br>

### Prediction Problem: Classification
We will build a classification model to predict the rating of a recipe. Since the correlation we got from the past hypothesis test, we chose `n_ingredients` and `calories` as two indicators to predict the rating of a recipe. Obviously, as we mentioned above, this will be a multi-class prediction model, not a binary one since there are 5 different possible ratings.<br>

### Response Variable
In this project, we have selected the `rating` of a recipe as the response variable. This choice is driven by its quantitative discrete nature, allowing for us straightforward classification. Additionally, the real-world significance of recipe ratings cannot be overstated, as they play a crucial role in guiding consumer choices and influencing restaurant offerings. Accurately classifying a recipe's rating, especially distinguishing between a 5-star and a 1-star rating, is vital for understanding customer preferences and trends.<br>

### Evaluation Metrics
For our classification model, we could assess its performance using metrics such as accuracy, F1 score, precision, or recall. Given our focus on health-conscious trends in recipe ratings, precision will be our key metric. Besides, we will also track the F1 score to confirm whether our prediction model is even.<br>

**Justification**<br>
The choice of metrics reflects our goal to accurately identify recipes that align with calories amount and number of ingredients profiles. Precision is crucial as it minimizes the risk of incorrectly identifying a lower-quality recipe as high-quality, which is vital in the context of recommending health-conscious recipes to users. Moreover, the F1 score is chosen over accuracy as a more relevant metric due to its balanced focus on precision and recall. This metric is particularly useful when we need to strike a balance between the precision of our model and its ability to recall or identify all relevant instances of high-rated recipes. The F1 score is critical in scenarios where both the false positives and false negatives are of concern, ensuring that our model not only accurately identifies high-quality recipes but also doesn't miss out on any potential recipes that could meet our users' health-conscious standards.<br>

### About Data Cleaning
Our exploratory data analysis on this dataset can be found [here](https://acai1031.github.io/-Recipes-Research-Project/)<br>

Except the data cleaning steps we took above, we have two more modification.<br>
1. We use a different technique to handle missing value of `rating` and `review`. We notice that if the user neither leave a review nor a rating, then both of them will be filled with np.nan, while if the user leave a review without rating, the rating will be 0. Following this observation, we firstly fill all ratings of 0 with np.nan, then drop all rows that contains np.nan in review or np.nan in ratings, because we believe the missingness of these two variables might due to random chance.<br>
2. In order to better represent the user attitude towards recipe, We transform the `review` into a column `sentiment` labeled with 'negative', 'neutral', and 'positive'. 

---

## Baseline Model
### Description
We use Decision Tree model in this prediction task. The selected features for the model are `n_ingredients`, and `calories`. We choose these features because we hypothesize that a recipe with diverse ingredients and low calories will have a higher rating from people due to the health awareness nowadays, and the scatterplot below revealing the relationship between n_ingredients, calories, and rating.

<iframe src="assets/Relationship between n_ingredients, calories, and rating.html" width=900 height=600 frameBorder=0></iframe> <br>

In specific, `n_ingredients` is a discrete quantitative feature representing the number of ingredients involved in each recipes. It is a numerical variable. `calories` is a continuous quantitative feature representing the value of calories for this food. It is a numerical variable. <br>

### Feature Transformations
We apply three transformations on these features. Firstly, due to the right-skewed nature of `n_ingredients` followed below, we apply a square root transformation to the `n_ingredients` column since it is less aggressive than the logarithmic transformation and can be useful for variables with moderately right-skewed distributions.<br>

<iframe src="assets/Distribution_of_Number_of_Ingredients.html" width=900 height=600 frameBorder=0></iframe> <br>

For `calories`, according to the wide-spread and uniformative attribute of this variable, we apply a Binarizer transformation to `calories` with threshold equals to the mean of calories, allowing us to map this quantitative sequence to a sequence of 1s and 0s.<br>

And finally, the model use StandardScaler to normalize both features to avoid inconvience resulted from the inconsistency of calories units and n_ingredients units.<br>

### Performance
The model achieved a training precision of 0.818 and a testing precision of 0.657. The F1-scores for the model are 0.819 and 0.674, respectively. Based on the precision and F1-score performance metrics, we may say that the current model is not good enough. The model demonstrates a high level of precision on the training data, particularly important when predicting recipe ratings. Although the testing precision is slightly lower than the training precision, it remains at a reasonably high level. The model maintains its ability to make accurate positive predictions on unseen data. The noticeable difference between training and testing precision (0.818 vs. 0.657) suggests a potential overfitting issue. <br>

The values within confusion matrix shown below provide a detailed breakdown of the model’s predictions, allowing for a deeper analysis of its performance. We find that the existing model lacks effectiveness as it is consistently predicting a 5-star rating. This behavior is likely attributed to the significant imbalance in the dataset. If the training data is imbalanced, meaning there is a significant difference in the number of observations between different ratings, the model may prioritize the majority class which is 5-star rating and struggle to generalize to minority classes.<br>

<iframe src="assets/confusion_matrix.html" width=900 height=600 frameBorder=0></iframe>

---

## Final Model
### Model Choosing 
After performing multiple experiments, we have opted for the Random Forest Classifier as our chosen model for two primary reasons. Firstly, while the Decision Tree model serves well as a baseline, its performance lacks consistency and stability when compared to alternative models. This inconsistency poses challenges for us in the effective fine-tuning of the final model. Secondly, the Random Forest, being an ensemble model composed of multiple decision trees, provides aggregated predictions that assist in addressing the influence of imbalanced `rating` in our dataset. This characteristic suggests that this classifier is a more favorable choice. <br>

### Feature Transformations
Besides the features we used in baseline model, here are the additional selected features for our model:
- `categorical_sentiment`: This feature is transformed using `OneHotEncoding`. It provides valuable information about the user attitude towards a recipe, as different sentiment categories can have distinct patterns or effects on the predicted rating of recipe. 
- `quantile_avg_rating`: This is a continuout quantitative variable, and we use `QuantileTransformer` to transform this feature to follow a uniform distribution. This feature represents the average rating of recipes. It may be relevant for predicting the rating of recipe, as it represents some characteristic of the recipe and smooth the effect of outliers on overall rating. 
- `standardize_nutrition`: These three features `saturated fat`, `protein`, `carbohydrates` are scaled by `StandardScaler`. These features represent the nutrition content of the recipe. Scaling is applied for units consistency and to prevent dominance by features with larger values. There appear to be a connection between nutritions and the ratings. This aligns with intuition – individuals tend to favor foods with carbohydrates for a boost in happiness and satisfaction. Additionally, saturated fat and protein are essential nutrients in our diet, and people may express higher ratings for foods containing these nutrients.
<br>

### Choosing hyperparameter
- We used `GridSearchCV` with 5 folds to find the optimal hyperparameters. Due to the time efficiency, we run and comment the code for finding the best hyperparameter, and leave the result within the comment.
- Hyperparameters: We tuned a combination of the max depth, and number of estimators for the Random Forest to find a combination that led a model that generalized the best to unseen data (i.e. performs well on the test set). The optimal hyperparameters we found were a max_depth = 10, n_estimators = 100.

### Performance
This tuning demonstrates a higher precision and F1-score on both the training and testing sets compared to the first model. This suggests an improvement in the model's ability to generalize to new data while maintaining strong performance on the training set, because of the additional feature engineering. The model shows consistency in performance across both metrics on the training and testing sets. <br>

Moreover, the examination of the confusion matrix reveals that our model diverges from the tendency of consistently predicting a 5-star rating, unlike the baseline. This is evident in the increased precision, as reflected in the augmented count of diagonal elements. Consequently, our model demonstrates enhanced generalization to unseen data according to our evaluation metrics, aligning with our goal for improvement supported by measurable metrics.<br>

<iframe src="assets/confusion_matrix_final.html" width=900 height=600 frameBorder=0></iframe>

---

## Fairness Analysis
### Fairness group choice
The research question we intend to examine is whether precision is consistent when we predict rating with diverse ingredients and with simple ingredients. Do groups with simple ingredient (low ‘n_ingredients’) have better performance in our final prediction model from the ones with diverse ingredient? <br>

### Divided Groups
The mean of `n_ingredients` is around 9, so we consider `n_ingredients` <= 9 as simple and `n_ingredients` > 9 as diverse.

**Null Hypothesis (H0)**: Our model is fair. Its precisions for simple ingredients recipe and diverse ingredients recipe are roughly the same, and any differences are due to random chance.<br>
**Alternative Hypothesis (H1)**: Our model is unfair. Its precision for simple ingredients recipe is better than its precision for diverse ingredients recipe.<br>
**Test statistic**: Difference in precision (simple group minus diverse group)<br>
**Significant level**: 0.05 <br>


<iframe src="assets/permutation_test.html" width=900 height=600 frameBorder=0></iframe> <br>
The plot above shows the empirical distribution of our test statistics in 1000 permutations, the red line indicates the observed test statistics.<br>

Based on our test statistics, p-value is **above 0.05**, and this graph, the difference in accuracy across the two groups seems significant.
Therefore, we **fail to reject** our null hypothesis and claim that simple ingredients recipe and diverse ingredients recipe might have the same precision. Our prediction model is fair on both groups which have the same level of performance
<br>



