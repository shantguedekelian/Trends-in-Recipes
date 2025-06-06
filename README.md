<!-- MathJax Support -->
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

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

<br>

The histogram shows the empirical distribution of the test statistic (difference in mean number of ingredients between missing, and not missing descriptions), along with the observed statistic in red.

<br>
<iframe
  src="assets/mar_perm_test (2).html"
  width="900"
  height="600"
  frameborder="0"
></iframe>

<br>

The plot shows that the description column is definitely dependent on the number of ingredients column, making it's type of missingness MAR.


# Hypothesis Testing
### Question: Are certain tags or ingredients becoming more popular (e.g., “high-protein”)?

> **Null Hypothesis (H0):** The proportion of recipes with the **'high-protein'** tag has not increased after 2016.
>
> **Alternate Hypothesis (H1):** The proportion of recipes with the **'high-protein'** tag has increased after 2016.

I will conduct a one-tailed permutation test to assess whether the difference in proportions is statistically significant.


**Significance Level:** *P = 0.05*

**Test Statistic:** the difference in means between the proportion of **'high_protein'** tag after 2016 and before 2016. 

$$
\left( \frac{\text{# high_protein}_{>2016}}{\text{total # recipes}_{>2016}} \right) -
\left( \frac{\text{# high_protein}_{\leq2016}}{\text{total # recipes}_{\leq2016}} \right) 
$$

The choice of test statistic is valid since we only care about a positive difference in means. Also because in the hypothesis we are dealing with difference in proportions. And difference in proportions is a good metric of popularity because it shows how popular it was compared to other tags.

<br>

**Figure 4:** the histogram shows the distribution of the test statistic.

<iframe
  src="assets/protein_perm_test.html"
  width="900"
  height="600"
  frameborder="0"
></iframe>

<br>

Our observed statistic is really far to the right and with a **p-value of <0.01**, we can conlude that our observed difference in proportions is **statistically significant**. Meaning that we **reject** the Null Hypothesis and conclude that there is a really likely **increase** in popularity for the **'high_protein'** tag after 2016.

# Framing a Prediction Problem
**Prediction Problem:**
Can we predict how much protein a recipe contains, based on its other nutritional and descriptive features?

Specifically, we'll try to predict the **'protein_pdv'** (percent daily value of protein) using features such as:

>**Nutrition Info:** 'calories', 'total_fat_pdv', 'sugar_pdv', 'carbohydrates_pdv'
>
>**Tags**: 'meat', 'high-protein', 'vegetarian', etc. extracted from the tags column
>
>**Other Columns:** 'n_steps' and 'n_ingredients'

This is a **regression problem**, as we're predicting a continuous numeric value.
It could be useful for health-conscious users or dietary planning apps that need protein estimates without full nutritional data.

The metric I will use to evaluate my model will be R² because it provides a clear measure of how well our features explain the variability in the target variable (protein_pdv). Unlike RMSE or MAE, which are in the units of the target variable and harder to interpret directly, R² is a unitless, relative metric that tells us how much better our model. This makes it especially useful for comparing models and understanding overall performance. I considered RMSE and MAE, but chose R² for its interpretability and its ability to convey how much variance in protein_pdv our model captures.

# Baseline Model
**Features in my Baseline Model:**

>**Features:**
>
>`calories` *(Quantitative)*: The number of calories per serving of the recipe.
>
>`is_meat` *(Nominal)*: A binary feature (0 or 1) indicating whether the recipe contains the tag 'meat'.

>**Target:**
>
>`protein_pdv`: The percent daily value of protein in a serving of the recipe.

**Model:**

>**Linear Regression Model:** Chosen for its simplicity and interpretability as a starting point.

**Performance:**

The baseline model achieved an **R² value of 0.49**, which indicates limited explanatory power. This performance suggests that:

>The model is likely **underfitting**, due to a lack of informative features.
>
>**Linear Regression** may not be the most suitable model for capturing nonlinear patterns in this dataset.
>
>**Feature transformations** and **engineering additional predictors** may be necessary to improve results and interpretability.


# Final Model
### Model Overview:

For the final model, I expanded the feature set and selected a more flexible algorithm to better capture nonlinear relationships in the data.

### Features in my Final Model:

>**Nutrition Info:** I included additional nutritional features under the hypothesis that different macronutrients may have predictive value for protein content.
>
>`calories` *(Quantitative)*: The number of **calories** per serving of the recipe.
>
>`total_fat_pdv` *(Quantitative)*: The percent daily value of **total fat** in a serving of the recipe.
>
>`sugar_pdv` *(Quantitative)*: The percent daily value of **sugar** in a serving of the recipe.
>
>`carbohydrates_pdv` *(Quantitative)*: The percent daily value of **carbohydrates** in a serving of the recipe.

>**Additional Complexity Features:** These features were included to capture potential relationships between recipe complexity and protein content.
>
>`n_steps` *(Quantitative)*: The number of **steps** in a recipe.
>
>`n_ingredients` *(Quantitative)*: The number of **ingredients** in a recipe.

> **Tag-Based Features:** I added binary features based on the top tag correlations with `protein_pdv`. These were one-hot encoded from the `tags` column.
>
>`is_meat` *(Nominal)*: A binary feature (0 or 1) indicating whether the recipe contains the tag 'meat'.
>
>`is_vegetarian` *(Nominal)*: A binary feature (0 or 1) indicating whether the recipe contains the tag 'vegetarian'.
>
>`is_high_protein` *(Nominal)*: A binary feature (0 or 1) indicating whether the recipe contains the tag 'high-protein'.
>
>`is_main_dish` *(Nominal)*: A binary feature (0 or 1) indicating whether the recipe contains the tag 'main-dish'.
>
>`is_poultry` *(Nominal)*: A binary feature (0 or 1) indicating whether the recipe contains the tag 'poultry'.
>
>`is_chicken` *(Nominal)*: A binary feature (0 or 1) indicating whether the recipe contains the tag 'chicken'.
>
>`is_beef` *(Nominal)*: A binary feature (0 or 1) indicating whether the recipe contains the tag 'beef'.

>**Target:**
>
>`protein_pdv`: The percent daily value of protein in a serving of the recipe.

**Model:**

>**Decision Trees Model:** A decision tree model was chosen for its ability to capture nonlinear patterns and interactions between variables. It also offers interpretability and handles both quantitative and binary features effectively.

### Feature Engineering:

To enhance predictive power, I explored the tags column by one-hot encoding each tag and calculating its correlation with the target variable, protein_pdv.

Below is a table of the top tags most positively correlated with protein content:

|                   |   Correlation |
|:------------------|--------------:|
| protein_pdv       |      1        |
| main-dish         |      0.424862 |
| meat              |      0.423562 |
| poultry           |      0.286588 |
| high-protein      |      0.264706 |
| chicken           |      0.264121 |
| high-in-something |      0.205527 |
| beef              |      0.186752 |
| pork              |      0.183208 |
| chicken-breasts   |      0.178225 |

These tags were added as binary indicator features (1 if present, 0 otherwise) to the model to improve its ability to distinguish high-protein recipes.

### Normalization:

To address skewed distributions and outliers, I applied normalization to the following quantitative features: `calories`, `total_fat_pdv`, `sugar_pdv`, `carbohydrates_pdv`, `n_steps`, and `protein_pdv`.

These columns showed significant right-skew, with extreme values that could negatively affect model performance and interpretation. Below is an example of the original distribution of the `calories` column.

<br>

**Figure 5:** Histogram showing the original distribution of the `calories` feature.

<iframe
  src="assets/skewed_calories_dist.html"
  width="900"
  height="600"
  frameborder="0"
></iframe>

<br>

To normalize the data, I applied a logarithmic transformation, which is particularly effective for reducing skew and compressing the impact of large outliers. Below is the resulting distribution of `calories` after applying the transformation:

<br>

**Figure 6:**  Histogram showing the distribution of `calories` after log transformation.

<iframe
  src="assets/transformed_calories_dist.html"
  width="900"
  height="600"
  frameborder="0"
></iframe>

<br>

The transformed distribution is much closer to normal, which helps improve model stability and interpretability.


### Grid Search for best hyperparameters

# Fairness Analysis

