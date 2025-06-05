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
**Figure 1** presents the distribution of **calories per serving**, giving insight into the overall nutritional density of the recipes.

<br>
<br>

<iframe
  src="assets/calories_dist (2).html"
  width="1000"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis:
aadasd

### Interesting Aggregrates:
asdasd


# Assessment of Missingness


# Hypothesis Testing


# Framing a Prediction Problem


# Baseline Model


# Final Model


# Fairness Analysis

