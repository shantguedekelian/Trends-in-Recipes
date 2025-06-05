# **Trends in Recipes** 
by Shant Guedekelian

this is a project for DSC 80 at UCSD

# Introduction
This project explores trends in online recipes over time using a dataset from Food.com, which includes thousands of user-submitted recipes and reviews. The central question we aim to answer is: **Have high-protein recipes become more popular in recent years?**

With the rise of fitness culture, dietary tracking apps, and growing awareness about nutrition, protein has taken center stage in many people’s diets. By analyzing whether recipes emphasizing protein are being submitted more frequently, we can get a window into shifting food priorities, what people value in meals, and how cultural trends around health and fitness are shaping home cooking.

This is a trend that is important to more than just health experts as it reflects how everyday people are thinking about their meals and making choices in the kitchen.

The dataset that I am using contains **234,429 rows**, each corresponding to a review written about a recipe. In total there are **83,782 unique** recipes in my dataset. 

Some relevant columns are:
>**'submitted':** Date recipe was submitted.
>
>**'tags':** Food.com tags for recipe (gluten free, etc).
>
>**'nutrition':** Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value”.
>
>**'n_ingredients':** Number of ingredients in recipe.
>
>**'n_steps':** Number of steps in recipe.
>
>**'date':** Date when review was submitted.


# Data Cleaning and Exploratory Data Analysis
First of all, I had to merge two datasets. One contained all of the recipes information like the tags, nutrition info, and ingredients; the other contained all of the information regarding user feedback such as the date the review was submitted. 

Then using the merged dataframe, I filled in the ratings of 0 with null since ratings usually start at 1, and I wouldn't want the zeroes to affect my future results. I created a new column named 'mean_rating' which represents the mean rating per recipe.

Using the 'nutrition' column of the merged dataset, I added 7 columns, one for each nutrient information (calories, sugar pdv, carbohydrate pdv, etc).

### Univariate Analysis:
**Figure 1** represents the distribution of **calories per serving**, giving insight into the overall nutritional density of the recipes.

<br>
<br>

<iframe
  src="assets/calories_dist (3).html"
  width="900"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis:
**Figure 2** represents the relationship between of **calories and total fat**, giving insight into how calorie-dense fat is.

<br>

<iframe
  src="assets/cal_v_totalfat (2).html"
  width="900"
  height="600"
  frameborder="0"
></iframe>

<br>

This scatter plot shows a strong correlation (an **R<sup>2</sup>** value of **0.74**) between the two variables as shown by the trendline. This makes sense as fat is 7 calories per gram, making the densest nutrient in calories.

### Interesting Aggregrates:
The mean calories for each number of ingredients.

<br>

|   n_ingredients |   calories |
|----------------:|-----------:|
|               1 |   1151.86  |
|               2 |    395.96  |
|               3 |    283.122 |
|               4 |    305.826 |
|               5 |    331.26  |
|              ...|         ...|
|              30 |    678.464 |
|              31 |    872.454 |
|              32 |    864.475 |
|              33 |    338.2   |
|              37 |  10687.7   |

<br>

As we see, there is an overall increase in calories as the number of ingredients increases; suggesting that more complex meals might be correlated with higher calories.


# Assessment of Missingness
### NMAR Analysis:
There are only 3 columns in the dataset that contain NaN values; they are **description**, **rating**, **review**.
Lets take a look at each one.

> **Description:** Not NMAR, most likely MAR since the probability of a recipe not having a description likely depends on how complex a recipe is.
>
> **Rating:** NMAR since ratings tend to be extremely positive or negative. If the review is more in the middle, there is a lower probability of the user writing it. People are more likely to leave a review when they really loved a recipe, or they really hated it.
>
> **Review:** NMAR for the same reason as Rating column. People are more likely to leave a review when they really loved a recipe, or they really hated it.

### MAR Analysis:
To determine if the missing values in **description** column are MAR, **I will perform a permutation test to see if the probability of null values in the 'description' column is dependent on 'n-ingredients' and unrelated to 'sugar_pdv'**

> n_ingredients: Likely related to 'description' column (more complex recipes might get more explanation).
>
> sugar_pdv: Likely not related to 'description' column (nutritional info is calculated).

The plot below shoes the difference in mean number of ingredients between missing, and not missing descriptions.

<br>

<iframe
  src="assets/mar_perm_test.html"
  width="900"
  height="600"
  frameborder="0"
></iframe>

<br>

# Hypothesis Testing


# Framing a Prediction Problem


# Baseline Model


# Final Model


# Fairness Analysis

