Author: Josh Osofsky
## Introduction
Whether it be for a first date, a Thanksgiving feast, or simply for yourself, food recipes play a critical role in people's everyday lives. Usually, the main focus of any given recipe is the star protein of the dish, however, not all recipes have the same amount of protein with some having none at all. This brings to light an interesting question: **What is the relationship between a recipe's protein level and its average rating?** This question ought to be answered since a recipe's protein level may be correlated with lower or higher ratings depending on individual dietary preferences and health concerns. To answer this question, I employed the use of two data sets from [food.com](https://www.food.com/); the first data set contains recipes and the second comprises user ratings of specific recipes.

The first data set named `recipes` has 83782 rows each representing a unique recipe and 10 columns with the following descriptions:

| Column | Description |
| ----------- | ----------- |
| `name` | Name of the recipe |
| `id` | Unique ID of the recipe |
| `minutes` | Time in minutes estimated to complete recipe |
| `contributer_id` | Unique ID of the user who submitted the recipe |
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
