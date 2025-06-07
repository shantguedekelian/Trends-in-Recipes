<!-- MathJax Support -->
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>


# Introduction
This project explores trends in online recipes over time using a dataset from Food.com, which includes thousands of user-submitted recipes and reviews. The central question we aim to answer is: **Have high-protein recipes become more popular in recent years?**

With the rise of fitness culture, dietary tracking apps, and growing awareness about nutrition, protein has taken center stage in many people’s diets. By analyzing whether recipes emphasizing protein are being submitted more frequently, we can get a window into shifting food priorities, what people value in meals, and how cultural trends around health and fitness are shaping home cooking.

This is a trend that is important to more than just health experts as it reflects how everyday people are thinking about their meals and making choices in the kitchen.

The dataset that I am using contains **234,429 rows**, each corresponding to a review written about a recipe. In total there are **83,782 unique** recipes in my dataset. 

Some relevant columns are:
>`submitted`: Date recipe was submitted.
>
>`tags`: Food.com tags for recipe (gluten free, etc).
>
>`nutrition`: Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value”.
>
>`n_ingredients`: Number of ingredients in recipe.
>
>`n_steps`: Number of steps in recipe.
>
>`date`: Date when review was submitted.


# Data Cleaning and Exploratory Data Analysis
To begin I merged two datasets. 

>One contained recipe information such as **tags, ingredients, and nutrition facts.**
>
>The other included **user-generated reviews**, including timestamps and ratings.

After merging, I performed the following cleaning steps:

>**Replaced all 0-star ratings with null values**, assuming that ratings start at 1 and that zeroes likely represent missing data.
>
>**Calculated a new column mean_rating** to represent the average rating per recipe, which helped summarize user feedback effectively.

Next, I parsed the **nutrition column**, which originally stored all nutrient information as a list. I split this into **seven separate columns**, each representing a specific nutritional component such as: 
>`calories`
>
>`total_fat_pdv`
>
>`sugar_pdv`
>
>`carbohydrate_pdv`
>
>`protein_pdv`
>
>*... and others*

### Univariate Analysis:

**Figure 1** represents the distribution of **calories per serving**, giving insight into the overall nutritional density of the recipes.

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

This scatter plot reveals a strong positive correlation between the two variables, with an **R² value of 0.74**, as indicated by the trendline. This relationship is intuitive as **fat contains 9 calories per gram**, making it the most calorie-dense macronutrient.

### Interesting Aggregrates:
Below is the **mean number of calories per recipe** grouped by the number of ingredients:


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

We observe a **general upward trend** in calories as the number of ingredients increases. This suggests that **more complex recipes** (with more ingredients) may tend to be higher in calories, potentially due to the inclusion of richer or more calorie-dense components.


# Assessment of Missingness
### NMAR Analysis:
There are only three columns in the dataset that contain missing values: `description`, `rating`, and `review`. Let's analyze the likely **missingness mechanism** for each:

>**NMAR (Not Missing at Random):**
>`rating`: Likely NMAR. Ratings tend to be more commonly left when users feel strongly, **either very positively or very negatively** about a recipe. More neutral experiences are less likely to result in a submitted rating.
>
>`review`: Also likely NMAR, for the same reasons as `rating`. People are more likely to leave a review when they really loved a recipe, or they really hated it.

>**MAR (Missing at Random):**
>
>`description`: Likely MAR. The likelihood that a recipe is missing a description may depend on observable features such as **recipe complexity**. For instance, simpler recipes might not require a description, whereas more involved recipes might have more detailed instructions.

### MAR Analysis:
To evaluate whether the missingness in the `description` column is truly **MAR**, I performed a **permutation test** to examine if the probability of missing descriptions is dependent on observable features:

> `n_ingredients`: Likely related to `description` column (more complex recipes might get more explanation).
>
> `sugar_pdv`: Likely not related to `description` column (nutritional info is calculated).

<br>

**Figure 3** The histogram below shows the **empirical distribution** of the test statistic (the difference in mean number of ingredients between recipes with and without descriptions). The observed statistic is marked in **red**.

<br>
<iframe
  src="assets/mar_perm_test (2).html"
  width="900"
  height="600"
  frameborder="0"
></iframe>

<br>

The plot demonstrates a clear dependence between the `description` column and the `n_ingredients` feature, supporting the conclusion that the missingness in description is **Missing At Random (MAR)**.


# Hypothesis Testing
### Question: Are certain tags or ingredients becoming more popular (e.g., “high-protein”)?

> **Null Hypothesis (H0):** The proportion of recipes with the **'high-protein'** tag has not increased after 2016.
>
> **Alternate Hypothesis (H1):** The proportion of recipes with the **'high-protein'** tag has increased after 2016.

I will conduct a one-tailed permutation test to assess whether the difference in proportions is statistically significant.


**Significance Level:** *α = 0.05*

**Test Statistic:** The **difference in proportions** of recipes tagged as `high_protein` before and after 2016: 

$$
\left( \frac{\text{# high_protein}_{>2016}}{\text{total # recipes}_{>2016}} \right) -
\left( \frac{\text{# high_protein}_{\leq2016}}{\text{total # recipes}_{\leq2016}} \right) 
$$

This test statistic is appropriate because it directly measures the change in popularity of the tag as a proportion of all recipes in each period. A one-tailed test is justified because we are only interested in detecting an **increase** in popularity.

<br>

**Figure 4:** The histogram shows the empirical distribution of the test statistic under the null hypothesis. The observed value is marked in red.

<iframe
  src="assets/protein_perm_test.html"
  width="900"
  height="600"
  frameborder="0"
></iframe>

<br>

**Conclusion:**

Since the observed statistic lies far in the right tail of the distribution, and the **p-value is < 0.01**, the result is **statistically significant**.

We therefore **reject the null hypothesis** and conclude that the `high_protein` tag has **increased in popularity after 2016**

# Framing a Prediction Problem
**Prediction Problem:**
Can we predict how much protein a recipe contains, based on its other nutritional and descriptive features?

Specifically, we'll build a regression model to predict a continuous target variable `protein_pdv` (% daily value) based on the following:

>**Nutrition Features:** `calories`, `total_fat_pdv`, `sugar_pdv`, `carbohydrates_pdv`
>
>**Tags (from the `tags` column)**: `meat`, `high-protein`, `vegetarian`, etc. extracted from the `tags` column
>
>**Other Descriptive Features::** `n_steps` and `n_ingredients`

This problem is relevant to health-conscious users and apps that aim to estimate protein content when full nutrition data isn’t available.

**Evaluation Metric:**

I chose **R²** as the primary evaluation metric because:

>It measures the **proportion of variance** in `protein_pdv` explained by the model.
>
>It’s **unitless and more interpretable** than RMSE or MAE when comparing model performance.
>
>It provides a clearer sense of how well the features capture the **structure of the data**.

I considered RMSE and MAE, but chose R² for its **interpretability** and its ability to convey how much variance in `protein_pdv` our model captures.

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


### Grid Search for best hyperparameters:

To optimize the performance of the Decision Tree Regressor, I used GridSearchCV to tune key hyperparameters. Specifically, I searched over the following parameter grid:

>`'max_depth': [3, 5, 10, 20, None]`
>
>`'min_samples_leaf': [1, 2, 4]`
>
>`'min_samples_split': [2, 5, 10]`

These hyperparameters were chosen to help control the complexity of the tree and reduce the risk of overfitting, which Decision Trees are particularly susceptible to.

Based on the grid search, the best-performing combination was:

>`'max_depth': 20`
>
>`'min_samples_leaf': 4`
>
>`'min_samples_split': 10`

### Model Results:

The model achieved a **cross-validated R² of 0.837**, suggesting strong predictive performance on unseen data. When **refit** on the **full training set**, the R² rose to **0.976**, indicating a very close fit to the training data, which may hint at slight overfitting. 

This represents a **significant improvement** over the baseline model, which only achieved a **cross-validated R² of 0.49**. The added features, log transformations, and optimized hyperparameters contributed to a more robust and accurate model.

# Fairness Analysis

**Research Question:** 

Is there a significant difference in model performance between recipes labeled with **'is_beef'** and those labeled with **'is_chicken'**?

>**Null Hypothesis:** The model performs equally well for both categories. Any observed difference in **RMSE** between **beef** and **chicken** recipes is due to random variation.
>
>**Alternate Hypothesis:** The model's performance **differs significantly** between the two groups. Specifically, it has a **higher** or **lower RMSE** for **beef** recipes compared to *chicken* recipes — suggesting potential **bias or unfairness**.

**Evaluation Metric:** 

Root Mean Squared Error (RMSE) is used to compare performance, as it is sensitive to **large prediction errors**. A higher RMSE indicates the model is making larger mistakes, which would highlight if it’s systematically underperforming for one group.

**Test Statistic:** 

I used the **difference in RMSE** between the `is_chicken` and `is_beef` recipe groups as the test statistic.

**Result:** 

The resulting **p-value was 0.80**, which is well above the typical significance threshold of **α = 0.05**. This means that the observed difference in RMSE is likely due to **random chance**, and not statistically significant.

Therefore, we **fail to reject the null hypothesis**. There is **no strong evidence** that the model performs differently for chicken vs. beef recipes, suggesting it may be fair with respect to these two categories.


