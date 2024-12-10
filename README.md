Author: Josh Osofsky
## Introduction
Whether it be for a first date, a Thanksgiving feast, or simply for yourself, food recipes play a critical role in people's everyday lives. Usually, the main focus of any given recipe is the star protein of the dish, however, not all recipes have the same amount of protein with some having none at all. This brings to light an interesting question: **What is the relationship between a recipe's protein level and its average rating?** This question ought to be answered since a recipe's protein level may be correlated with lower or higher ratings depending on individual dietary preferences and health concerns. To answer this question, I employed the use of two data sets from [food.com](https://www.food.com/); the first data set contains recipes and the second comprises user ratings of specific recipes.

The first data set named `recipes` has 83782 rows each representing a unique recipe and 12 columns with the following descriptions:

| Column | Description |
| ----------- | ----------- |
| `name` | Name of the recipe |
| `id` | Unique ID of the recipe |
| `minutes` | Time in minutes estimated to complete recipe |
| `contributer_id` | Unique ID of the user who submitted the recipe |
| `submitted` | Date that the recipe was submitted |
| `tags` | Tagged categories for the recipe |
| `nutrition` | Nutrition info for the recipe with the following order: [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)] |
| `n_steps` | Number of steps in the recipe |
| `steps` | The steps of the recipe in numbered order |
| `description` | Recipe description provided by the user |
| `ingredients` | Ingredients of the recipe |
| `n_ingredients` | Number of ingredients in the recipe |

The second data set named `ratings` has 731927 rows each representing a review left by a user and 5 columns with the following descriptions:

| Column | Description |
| ----------- | ----------- |
| `user_id` | Unique ID of the user leaving a review |
| `recipe_id` | Unique ID of the recipe |
| `date` | Date that the review was submitted |
| `rating` | Rating given to the recipe (1-5) |
| `review` | Review left on the recipe (if any) |

To answer the research question, I split all the values in the `nutrition` column into their own separate columns. The unit PDV (percent daily value) represents how much a nutrient in a serving of food contributes to a total daily diet. Using the `protein` and `calories` columns, I then also added another column named `prop_protein` which states the proportion of protein of the total calories in the recipe. Additionally, I also added `avg_rating` as a column which states the average rating of the recipe based on all its ratings. With all this information, I ascertained that the most relevant columns to answer the research question would be `calories`, `sugar`, `prop_protein`, `rating`, and `avg_rating` 

By conducting this research project, my findings could influence users on [food.com](https://www.food.com/) on what types of recipes to contribute based on a recipe's protein level and the associated relationship concerning the recipe's rating. 

## Data Cleaning and Exploratory Data Analysis

I conducted the following data-cleaning steps in order to make my data more efficient and easy to work with to answer my research question:
1. Performed a left merge on the `recipes` and `ratings` data sets and dropped the `recipe_id` column
   - Both data sets are now combined into one data frame via their shared `id` and `recipe_id` columns which now allows for seamless access to each recipe's ratings
2. Checked the data types of each column
   - I did this to assess which columns need to be further cleaned to better answer my research question
     
      | Column | Data Type |
      | ----------- | ----------- |
      | `name` | object |
      | `id` | int64 |
      | `minutes` | int64 |
      | `contributer_id` | int64 |
      | `submitted` | object |
      | `tags` | object |
      | `nutrition` | object |
      | `n_steps` | int64 |
      | `steps` | object |
      | `description` | object |
      | `ingredients` | object |
      | `n_ingredients` | int64 |
      | `user_id` | float64 |
      | `date` | object |
      | `rating` | float64 |
      | `review` | object |

3. Filled all ratings with a rating of 0 with np.nan
   - Ratings can only go as low as 1 and as high as 5 so ratings of 0 indicate a missing value rather than an actual rating of 0
4. Added the `avg_rating` column
   - This column states the average rating given to a recipe for all ratings made by unique users
5. Split the values in the `nutrition` column into their own individual columns
   - By splitting the `nutrition` column into several different columns, I would more easily be able to access the nutrition data as float objects rather than strings
6. Converted the `submitted` and `date` columns to datetime
   - Both these columns were strings originally so converting them to datetime makes accessing specific elements of the dates easier
7. Added the `is_labeled_vegetarian` column
   - This column states whether a recipe has 'vegetarian' as a label in its `tags` column. Vegetarian dishes tend to have less protein than non-vegetarian dishes so this column splits the recipes into two groups allowing for another method to compare high protein and low protein recipes
8. Added the `prop_protein` column
   - This column states the proportion of calories in the recipe that derive from a protein source. To calculate this, I divided the values in the `protein` column by 100%, multiplied them by 50 grams of protein ([food.com](https://www.food.com/) states that 50 grams of protein is the 100% PDV), multiplied them by 4 since there are 4 calories in 1 gram of protein, and finally divide by the total amount of calories in the recipe
  
#### Resulting DataFrame

| Column | Data Type |
| ----------- | ----------- |
| `name` | object |
| `id` | int64 |
| `minutes` | int64 |
| `contributer_id` | int64 |
| `submitted` | datetime64[ns] |
| `tags` | object |
| `nutrition` | object |
| `n_steps` | int64 |
| `steps` | object |
| `description` | object |
| `ingredients` | object |
| `n_ingredients` | int64 |
| `user_id` | float64 |
| `date` | datetime64[ns] |
| `rating` | float64 |
| `review` | object |
| `avg_rating` | float64 |
| `calories` | float64 |
| `total fat` | float64 |
| `sugar` | float64 |
| `sodium` | float64 |
| `protein` | float64 |
| `saturated fat` | float64 |
| `carbohydrates` | float64 |
| `is_labeled_vegetarian` | bool |
| `prop_protein` | float64 |

This cleaned DataFrame contains 234429 rows and 26 columns. Below are the first 5 rows of this new DataFrame named `clean_recipes` with only relevant columns:

| name                                 |     id |   minutes | submitted           |   rating |   avg_rating |   calories |   protein | is_labeled_vegetarian   |   prop_protein |
|:-------------------------------------|-------:|----------:|:--------------------|---------:|-------------:|-----------:|----------:|:------------------------|---------------:|
| 1 brownies in the world    best ever | 333281 |        40 | 2008-10-27 00:00:00 |        4 |            4 |      138.4 |         3 | False                   |      0.0433526 |
| 1 in canada chocolate chip cookies   | 453467 |        45 | 2011-04-11 00:00:00 |        5 |            5 |      595.1 |        13 | False                   |      0.0436901 |
| 412 broccoli casserole               | 306168 |        40 | 2008-05-30 00:00:00 |        5 |            5 |      194.8 |        22 | False                   |      0.225873  |
| 412 broccoli casserole               | 306168 |        40 | 2008-05-30 00:00:00 |        5 |            5 |      194.8 |        22 | False                   |      0.225873  |
| 412 broccoli casserole               | 306168 |        40 | 2008-05-30 00:00:00 |        5 |            5 |      194.8 |        22 | False                   |      0.225873  |

### Univariate Analysis

In order to better understand my research question, I analyzed the distribution of the proportion of protein in a recipe. As you can see in the below histogram, the distribution quickly increases then decreases peaking at around 10% proportion protein with an overall shape that is heavily skewed right suggesting that most recipes on [food.com](https://www.food.com/) have a low proportion of protein and that as the proportion of protein in a recipe increases, the amount of recipes on [food.com](https://www.food.com/) decreases. 

<iframe
  src="assets/univariate-analysis.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis

In order to better understand the relationship between the proportion of protein in a recipe and a recipe's rating, I analyzed the distribution between the rating a recipe received conditioned on whether or not [food.com](https://www.food.com/) labeled the recipe as 'vegetarian'. As you can see in the below bar plot, the distribution shows that recipes with ratings of 3 and 1 are more likely to be non-vegetarian recipes, recipes with a rating of 4 are more likely to be vegetarian recipes, and recipes with ratings of 5 and 2 are nearly equal in their probability of being and not being a vegetarian recipe. 

<iframe
  src="assets/bivariate-analysis.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates

While conducting my research, the relationship between the number of ingredients in a recipe and the proportion of protein in a recipe began to intrigue me. To better understand this, I grouped my data by number of ingredients and calculated the mean, median, minimum, and maximum proportion of protein for each as seen below.

| n_ingredients | mean_prop_protein | median_prop_protein | min_prop_protein | max_prop_protein |
|:--------------|:------------------|:--------------------|:-----------------|:-----------------|
| 1             | 0.11              | 0.10                | 0.00             | 0.35             |
| 2             | 0.12              | 0.07                | 0.00             | 0.83             |
| 3             | 0.10              | 0.06                | 0.00             | 0.87             |
| ...           | ...               | ...                 | ...              | ...              |
| 32            | 0.19              | 0.18                | 0.18             | 0.21             |
| 33            | 0.05              | 0.05                | 0.05             | 0.05             |
| 37            | 0.01              | 0.01                | 0.01             | 0.01             |

Upon closer inspection of this pivot table, there seems to be a minor relationship between the two variables. The mean, median, and minimum proportions of protein seem to increase steadily as the number of ingredients increases then decrease sharply while the maximum has an overall trend of decreasing steadily. This suggests that there is a slight relationship  between the number of ingredients in a recipe and that recipe's proportion of protein signifying that protein could be a primary aspect of a recipe's ingredient list and, therefore, quantity. 

## Assessment of Missingness

Of the columns in `clean_recipes`, three of them contain missing data: `date`, `rating`, and `review`. Due to this, I decided to assess the missingness of these columns

### NMAR Analysis

I believe that the `review` column is NMAR because whether or not someone decides to leave a review on a recipe likely hinges on whether said person had a strong reaction (positive or negative) to the recipe. For instance, if someone used a recipe and found that they really enjoyed it, they would be more likely to go out of their way to write up a review and post it. Conversely, if someone used a recipe and found that they really didn't enjoy it, they could also be more motivated to go out of their way to write up a review in order to warn other potential recipe users. 

### Missingness Dependency

Moving on, I decided to assess the missingness of the `rating` column. I did this by conducting permutation tests to evaluate whether `rating` could be MAR on `prop_protein` or on `n_ingredients` 

#### Proportion of Protein and Rating

**Null Hypothesis:** The missingness of ratings does not depend on the proportion of protein in the recipe

**Alternative Hypothesis:** The missingness of ratings does depend on the proportion of protein in the recipe

**Test Statistic:** The absolute difference of mean in the proportion of protein of the distribution of the group without missing ratings and the distribution of the group with missing ratings

**Observed Statistic:** 0.0039

**Significance Level:** 0.05

I ran the permutation test for this relationship by first adding a boolean column `rating_missing` stating whether `rating` is missing. I then shuffled this column 1000 times and compared the calculated test statistic to the original observed statistic each time. The results are shown below.

<iframe
  src="assets/permutation_test_histogram_1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**P-Value:** 0.0

As can be seen by the observed statistics' location on the distribution of the null values marked by the red line and the calculated p-value, **I reject the null hypothesis**. I believe that the missingness of the `rating` column is MAR on the `prop_protein` column. 

#### Number of Ingredients and Rating

**Null Hypothesis:** The missingness of ratings does not depend on the number of ingredients in the recipe

**Alternative Hypothesis:** The missingness of ratings does depend on the number of ingredients in the recipe

**Test Statistic:** The absolute difference of mean in the number of ingredients of the distribution of the group without missing ratings and the distribution of the group with missing ratings

**Observed Statistic:** 0.0181

**Significance Level:** 0.05

I ran the permutation test for this relationship by again using the column I created `rating_missing`. I then shuffled this column 1000 times and compared the calculated test statistic to the original observed statistic each time. The results are shown below.

<iframe
  src="assets/permutation_test_histogram_2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**P-Value:** 0.587

As can be seen by the observed statistics' location on the distribution of the null values marked by the orange line and the calculated p-value, **I fail to reject the null hypothesis**. I believe that the missingness of the `rating` column is not MAR on the `n_ingredients` column. 

## Hypothesis Testing

Circling back to my original research question, my main goal with this research project is to discern if people rate high-protein and low-protein recipes on the same scale. To categorize a recipe as having high or low protein, I decided to add another boolean column `high_protein` stating whether a recipe's `prop_protein` value is higher than the average `prop_protein` value. With this new column, I conducted a permutation test with the following hypotheses and test statistic

**Null Hypothesis:** Ratings of recipes are rated on the same set of standards and scale

**Alternative Hypothesis:** Ratings of recipes with high protein levels have a lower average rating than recipes without high protein levels

**Test Statistic:** The difference in average ratings of high protein recipes and low protein recipes.

**Observed Statistic:** -0.0233

**Significance Level:** 0.05

I decided not to run a hypothesis test because I don't have any information on a population to be sampled from and instead decided to run a permutation test because I'm more interested in determining whether the two distributions of high and low protein come from the same population. My prediction is that recipes with a higher proportion of protein are rated lower than recipes with a lower proportion since I believe that people may be wary of any adverse health effects that could arise from consuming too many protein-rich meals leading them to rate the recipe lower. For the test statistic, I opted to use the difference in the mean of the `rating` column for each distribution since my alternative hypothesis is directional and I'm concerned with determining whether low protein recipes typically get rated higher. 

<iframe
  src="assets/hypothesis_testing_histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**P-Value:** 0.0

#### Conclusion of Permutation Test

As can be seen by the observed statistics' location on the distribution of the null values marked by the purple line and the calculated p-value, I **reject the null hypothesis**. I believe that the ratings of recipes with high protein levels have a lower average rating than recipes without high protein levels. 

## Framing a Prediction Problem

For this section of my project, I plan to predict the average rating of a recipe which would be considered a classification problem because if I rounded each recipe's average rating to the nearest integer, I could treat the average rating of a recipe as an ordinal categorical variable with [1, 2, 3, 4, 5] as possible values. To do this, I will construct a multi-class classifier since there are multiple options for my model to predict from.

For my response variable, I chose a recipe's average rating because my overall research question is mostly concerned with how a recipe's rating is effected by other variables, therefore, I believe that this response variable would be a good representation of what I'm ultimately seeking to understand.

In order to evaluate my model, I plan to use the F1 score because of the left-skewed nature of the `rating` column with most ratings being centered around 4 and 5 stars. I chose not to use the accuracy because if I did the model would likely be biased and misleading due to the imbalanced classes.

The only information that I have access to in order to build this classifier is the columns within my `clean_recipes` data set. 

## Baseline Model

To start out, I split my data into training and test sets and then created a baseline model through the utilization of a random forest classifier. I used two features in this model: `prop_protein`, a quantitative numerical column, and `is_labeled_vegetarian`, a nominal categorical column. 

To train the model efficiently, I utilized a StandardScalar transformer on the `prop_protein` column and I one-hot-encoded the values in the `is_labeled_vegetarian` column via a FunctionTransformer and a lambda function. 

The F1 score of my model is 0.86 and the F1 scores of each of the rating categories are 0.17, 0.41, 0.46, 0.73, and 0.91 for rating scores of 1, 2, 3, 4, and 5 respectively. These scores tell me that my baseline model is better at predicting ratings of 4 and 5 compared to 1, 2, and 3. This is likely due to the left-skewed nature of the `rating` column since a vast majority of reviews fall within the 4 and 5 range, thus, increasing these categories' sample sizes and prediction accuracy. 

## Final Model

For my final model, I used the columns `is_labeled_vegetarian`, `minutes`, `calories`, `year` (a new column containing the year a recipe was submitted from the `submitted` column), and `prop_protein` as features.

#### `is_labeled_vegetarian`

This column of boolean values states whether a recipe is labeled as vegetarian. Including a column indicating whether a recipe is vegetarian provides valuable categorical information that can capture consumer preferences, as dietary choices often correlate with perceived recipe quality and ratings. This feature can help the model distinguish between rating patterns of vegetarian and non-vegetarian recipes, as these groups may appeal to different target audiences with distinct expectations. By incorporating this variable, the model gains an additional dimension of context about the recipe, which may improve its ability to predict average ratings accurately based on dietary restrictions or preferences. 

#### `minutes`

This column of float values states how long a recipe takes to make in minutes. Including the preparation time in minutes as a feature adds valuable context about the recipe's complexity and effort required, which can significantly influence user ratings. Recipes that take longer to make may be perceived as more elaborate or gourmet, potentially affecting their average ratings either positively or negatively depending on user preferences. By incorporating the cooking duration, the classifier model can better capture patterns and trends related to user satisfaction, thereby improving the accuracy of predicting average recipe ratings.

#### `calories`

This column of float values states how many calories are in a recipe. Including the calorie count as a feature provides insight into the recipe's nutritional profile, which could be a key factor influencing user preferences and average ratings. Recipes with higher or lower calorie counts may appeal to specific audiences, such as health-conscious individuals or those seeking indulgent meals, creating distinct rating patterns. By leveraging the calorie information, the classifier model can better capture relationships between nutritional content and user satisfaction, enhancing the accuracy of predicting recipe ratings.

#### `year`

This column states the year a recipe was posted to [food.com](https://www.food.com/). Including the year a recipe was posted provides temporal context, allowing the model to capture changes in culinary trends and user preferences over time that may influence ratings. Older recipes may have accumulated more reviews or different patterns of user feedback compared to newer ones, which can help the model identify temporal biases in ratings. By incorporating the posting year, the classifier can better understand how the age of a recipe affects its average rating, improving the accuracy of its predictions. 

#### `prop_protein`

This column states the percentage of calories that make up protein in a recipe. Including the proportion of protein in a recipe provides valuable nutritional information, which can influence user preferences and, consequently, the recipe's average rating. High-protein recipes may appeal to health-conscious users or those following specific dietary trends, leading to distinct rating patterns that the model can learn from. By leveraging the proportion of protein as a feature, the classifier can better capture the relationship between nutritional content and user satisfaction, improving the accuracy of its rating predictions.

For my modeling algorithm, I opted to use a RandomForestClassifier and used a GridSearchCV to tune its hyperparameters of `max_depth` and `n_estimators`. I chose these hyperparameters to tune because decision trees tend to have high variance, thus, tuning these hyperparameters can help reduce the variance to not overfit the training set. I found the best combination of hyperparameters to be 40 for `max_depth` and 200 for `n_estimators`. 

The F1 score of my final model is 0.92 which is a 0.06 increase from my baseline model. Additionally, the F1 scores of the rating categories are 0.33 for 1 star, 0.60 for 2 stars, 0.70 for 3 stars, 0.86 for 4 stars, and 0.95 for 5 stars which is a 0.16, 0.19, 0.24, 0.13, and 0.04 increase for each category respectively. 

## Fairness Analysis

For this final section, I split the recipes into two groups: high and low calorie recipes. I determined whether a recipe had a high or low amount of calories based on whether that recipe's calorie count exceeded or fell below the median calorie amount. I opted to use the median calorie amount over the mean calorie amount since a few outliers exist that would give a misleading average calorie amount. For the two groups, I decided to measure their precisions since I believe that it is more important to account for false positives over false negatives. In this context, a false positive would occur when the classifier incorrectly predicts the recipe to have a high/low average rating when in reality it has the opposite. This could be bad because the incorrect prediction could influence people to not select a recipe because of its low rating even though that recipe could be a perfectly healthy and delicious option. 

**Null Hypothesis:** The model is fair. The precision for recipes with higher vs. lower calories is around the same, with any differences being due to random chance.

**Alternative Hypothesis:** The model is unfair. The precision for lower-calorie recipes is lower than the precision for higher-calorie recipes.

**Test Statistic:** Difference in precision (low calories - high calories)

**Significance Level:** 0.05

**Observed Statistic**: 0.00175

**P-Value:** 0.232

To run this permutation test, I created a new boolean column `is_high_calories` which states whether a recipe has an above median calorie count. I then shuffled this column 1000 times and compared the calculated test statistic for each shuffling to the observed test statistic. I then calculated the above p-value. Because the p-value is above the significance level that I set, **I fail to reject the null hypothesis** and believe that my model is fair. I conclude that the model’s precision for recipes with lower calories is not necessarily lower than its precision for recipes with higher calories.
