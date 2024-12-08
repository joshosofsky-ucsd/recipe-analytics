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

To answer the research question, I split all the values in the `nutrition` column into their own separate columns. The unit PDV (percent daily value) represents how much a nutrient in a serving of food contributes to a total daily diet. Using the `protein` and `calories` columns, I then also added another column named `prop_protein` which states the proportion of protein of the total calories in the recipe. Additionally, I also added `avg_rating` as a column which states the average rating of the recipe based on all its ratings. With all this information, I ascertained that the most relevant columns to answer the research question would be `calories`, `sugar`, `prop_sugar`, `rating`, and `avg_rating` 

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
  
### Resulting DataFrame

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
