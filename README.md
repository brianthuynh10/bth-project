# Investigation of "High-Protein" Recipes and their healthiness

Author: Brian Huynh 

## Overview 

In this project, I am investigating proteins in the dataset and their possible relationships with ratings and the calories. In addition, I create a prediction model that is able to predict the number of calories in a recipe from nutritional factors such as sodium, fat, and protein

## Introduction 


Dataset #1: `recipe` contains 83,782 rows, each of them represetning a unique recipe, and 12 columns with the following information: 


| Column            | Description                                                      |
|:----------------- | :----------------------------------------------------------------|
| `'name'`           | Recipe name                                                    | 
| `'id'`             | Recipe ID                                                       |
| `'minutes'`        | Minutes to prepare recipe                                       |
| `'contributor_id'` | User ID who submitted this recipe                               |
|`'tags'`            | Food.com tags for recipe|
| `'submitted'`      | Date recipe was submitted
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe |
| `'steps'`          | Text for recipe steps, in order |      
| `'description'`    | User-provided description|                                                                                                            
| `'ingredients'`    | Text for recipe ingredients |                                                                    
| `'n_ingredients'`  | Number of ingredients in recipe |  

Dataset #2: `interactions` contains 731,927 rows, each of them being a review and rating for the recipes in the `recipe` dataset, and 5 columns being:

| Column            | Description                                                      |
|:----------------- | :----------------------------------------------------------------|
| `'user_id'`       | USER ID                                                          |
| `'recipe_id'`     | Recipe ID                                                        |
| `'date'`          | Date of interaction                                              |
| `'rating'`        | Rating given                                                     |
| `'review'`        | Review Text                                                     |


## Data Cleaning and Exploratory Data Analysis
50
In order to make the data useable, here are the following steps I completed to clean the dataset: 

1.  Performed a left merge on the recipes and interactions dataset on the id and recipe_id
   - The purpose of this step was to match the unique recipe ID with their respecitve ratings and review
  
2. Filled in all the missing ratings of 0 with np.nan
   - In the data, the ratings are ordinal, meaning that there is categories with a particular order, in the possible ratings are 1, 2, 3, 4, and 5. 1 being the lowest rating possible while 5 is best rating possible. Using this knowledge, we wouldn't want to fill in ratings with zero becasue that would bias the average rating of the recipe, it would be best to fill it in with np.nan
  
3. Add Column `avg_rating` for each recipe
   - It would be wise to include the average rating because there are many ratings per recipe and we wouldn't want one bad review define the entire recipe when there are other great reviews. Therefore, taking the average gives a better gist of the recipe

4. Split the values in the nutrition column  
   - The elements in the nutrition were originally objects though they appeared like lists. I utlized regular expressions and a lambda functions to ensure that the values would be split into 7 different values which are the nutritional values presented as presented above
   - The values that were incporated in this column were split into 7 float values
  
5. Covert submitted and date to datetime objects:
   - These two columns were origanlly stored as objects. Since the purpose of it was to be represented as a date. It would be smart to turn into a date time object if I plan on doing on plotting trends over time.
  
6. Outlier Removal of Calories and Protein
   - I decided to remove the outliers in the calories and protein columns because they would hinder the visualizations and wouldn't allow me to explore the data as well. This was done by using the IQR method.
  
7. Changed the Protein and Total Fat to Grams:
   - This purpose of this transformation will be useful for my investigation. To do this, we first assume that a person is on 2,000 calorie diet, we take the Perent Daily Value of the recipe multiply it by 50 and 65 for Protein and Total Fat, respecitively, then divide it by 100 to express the current percentage as a fraction.
  
8. Added a new column called `high_protein`
  - The column was created from the tags column which contains a string data type that I utilized to determine if the keyword 'high-protein' was in it. The purpose of this column is supposed to be boolean type with True/False to help to determine if the recipe is 'high-protein' or not.

9. Removal of the Reviews Column
   - The reviews column doesn't seem relevant to any of the plans I have for this project, so I will be removing it

**Result:** 
After cleaning the merged dataframe, I am left with 214965 rows and 26 columns. Here are the first couple rows with the columns that you should probably take a look at: 

| Name                             | id  | minutes| rating | avg_rating | calories (#) | protein (grams)| total fat (grams)| high_protein |
|:---------------------------------|:----|:-------|:-------|:-----------|:-------------|:----------------|:------------------|:-----------|
|1 brownies in the world best ever |333281|40      | 5.0 | 4.0        | 138.4        | 1.5              | 6.5              | False      |
|1 in canada chocolate chip cookies|453467|45      | 5.0 | 5.0        | 595.1        | 6.5              | 29.9             | False      |
|412 broccoli casserole            |306168|40      | 5.0 | 5.0         | 194.8        | 11.0             | 13.0              | False      |
|412 broccoli casserole            |306168|40      | 5.0 | 5.0         | 194.8        | 11.0             | 13.0              | False      |
|412 broccoli casserole            |306168|40      | 5.0 | 5.0         | 194.8        | 11.0             | 13.0              | False      |


### Univariate Analysis

In this analysis, I examined the distribution of the protein (in grams) in the recipes. In the plot below, one could determine that the distribution is skewed to the right. This suggests that most recipes on Food.com aren't extremely high in protein. WE could see that as we move more and more to the rigt the prescence of greater protein values is lessens

<iframe
src='Graphs/uni_protein.html'
width='800'
height='600'
frameborder="0"
></iframe>


### Bivariate Analysis

In this analysis, I wanted to explore the relationship between the Protein (in grams) and Calories. The graphs shows 3000 points from the data, if there more it'd be hard to see a possible relationship. In the scatter plot, there appears to be a pattern where as the amount of protein increases we start to see an increase in the number of calories. We could perhaps use this as varibale in a prediction model!

<iframe
src='Graphs/cal_pro_scatter.html'
width='800'
height='600'
frameborder="0"
></iframe>


### Interesting Aggregates

In this analysis, I grouped by the `high_protein` status and did aggregations like averages for total calores, total fat, total protein, minutes, and saturated fat (PDV). Doing this helped  uncover some patterns that one could possibly expect. 'High protein' recipes actually had higher total fat, calories, and saturated fat (PDV). Recipes that aren't classified as 'high protein' have longer average cooking time and tend to take up of a larger portion of you daily suggested amount of carbohydrates.

| `high_protein` | total fat (grams) | calories (#) | sodium (PDV) | minutes |  saturated fat (PDV) | carbohydrates (PDV)|
|:---------------|:------------------|:-------------|:--------------|:-----------|:-----------------|:------------------|
|False	         |15.10	           |316.53	     |24.37	       | 101.28	| 29.30	              |10.28              |
|True	            |19.19	           |351.64	      |24.74	    |84.47	   |33.47	              |3.55               |
 





## Assessment of Missingness
In the data there seems to be three columns of interest that have a significant amount of missing values - those columns are `description`, `rating`, and `avg_rating`. In this section, I will investigate the missingness of values in some the columns that contain missing values.

### NMAR Analysis 
I belive that the missigness of the `rating` column is potentially Missing Not at Random. The reason for this is that perhaps that people perhaps didn't enjoy the recipes as much didn't want to leave a bad rating, and the inverse could be true is that people who did enjoy the recipe would rather leave a rating. This could be the chance as most of the average ratings in the dataset are 3 and up while there aren't many that are rated lower than 2.

### Missingness Dependency
Furthering the investigation of missingness, I moved on to examine missingness of `rating` by testing to see if there are any columns that would depend on it. In this part, I checked to see if `rating` column is dependent on the `calories (#)` column, which is the total number of calories in the recipe. First I setup the hypothesis test with following hypotheses: 
- **Null Hypothesis:**  The missingness does not depend on the calories column
- **Altenativate Hypothesis:** The missingness does depend on the calories column
- **Test Statistic:** Absolue Difference of Means (Avg Amount of Calories with  `rating` missing - Avg Amount of Calories with  `rating` not missing)
- **Significance Level:** 0.01

<iframe
src='Graphs/missingness_test_dep.html'
width='800'
height='600'
frameborder="0"
></iframe>

**Analysis:** In this test, I created a column in the merged dataset marking if the row's rating was missing, called `missing_rating`, this will create two groups (True/False). Next I calcuated the observed test statistic which was 10.76, and began shuffling the `missing_rating` column then calculating the test statistic of the simulated data. This process was repeated 5,000 times, which created the visualization above. The p-value of this test came out to be 0.0 which is less than 0.01, so **we reject the null hypothesis**. Therefore, the missingness of ratings does depend on the `calories (#)` column.

Again, I'm going perform the same test to as above, except this time I will be testing to see if the missingness of `rating` depends on the `minutes` column. Here are the following hypotheses and test statistics for this test: 
- **Null Hypothesis:**  The missingness of `rating` does not depend on the `minutes` column
- **Altenativate Hypothesis:**  The missigness of `rating` does depend on the `minutes` columnn
- **Test Statistic** Absolue Difference of Means (Avg Amount of Minutes with  `rating` missing - Avg Amount of Minutes with  `rating` not missing)
- **Significance Level:** 0.01

<iframe
src='Graphs/missingness_test_independent.html'
width='800'
height='600'
frameborder="0"
></iframe>

**Analysis:** In this test, I did the same procedure as above - creating a column called `missing_rating`. The observered test statistic in the dataset is 38.255, and same process of shuffling `missing_rating` labels was done. The p-value of this test came out to be 0.1234 which is greater than the significance level I set, so **we fail to reject the null hypothesis**. Therefore, the missingness of ratings does NOT depend on the time to make/cook the recipe.


## Permutation Testing 
As sort of the main goal of the investigation, I am curious to see if high protein recipes could be as healthy as those that aren't labeled high protein. In this part, I will be performing a permutation test to help answer that question.

**Null Hypothesis:**  The average total amount of fat in 'high-protein' recipes and ones that aren't are the same and come from teh same distribution. Any observed difference is due to random chance.
**Alterantive Hypothesis:**The average total amount of for recipes is greater for recipes that are 'high-protein' and ones that aren't. Any observed difference is not due to random chance alone.
**Test Statistics:** Difference in Group Means (High-Protein Avg Fat - Non-High Protein Avg Fat)  
**Significance Level:** 0.01

As mentioned, I will be running a permutation test between the two groups. In this test, the value that we'll be looking at is total fat (in grams) because it's a value that people are always mindful of when trying to maintain a healthy lifestyle - heavy consumption fatty foods could lead to long-term health problems! So, total fat (grams) will be the variable of interest. As for the test statistic, I'm using difference of each groups average because the direction of this test matters, meaning that we actually want to see positive values which will capture if high protein recipe have greater average total fat than standard recipes. Using the absolute difference in group means fails to answer the question that I'm posing, the choice of difference in group mean better suits my investigation.

Testing Procedure: I'm utilzing the column `high_protein` again to divide into two different groups then calculating the test statistic, which is 4.09. Next, I'll shuffle the group labels then recalculated the test statistic for each group and do this 1000 times. 

<iframe
src='Graphs/hypo_test.html'
width='800'
height='600'
frameborder="0"
></iframe>


**Analysis of Permutation Test:** After 1000 simluted trials. The p-value that I found was 0.00, which is less than significance level, 0.01. This means we'll **reject the null hypothesis.** We can say that high protein recipes on average have more fat than standard recipes, but we can't be totally sure becasue this is just an observation there could be factors that make the high protein recipes fatter. The cut of meat used can vary such a fatty piece of steak or the cooking methods such as deep-frying could influence the amout of fat in the recipe, we can't exaclty conclude that high protein recipes will have a higher fat content on average.


## Framing Prediction Problem:

I plan to predict the number of calories in a recipe using a regression model. The reason for choosing calories as the target variable is that it is a standard value calculated for food products that we consume daily. The total calories in a food item are primarily determined by the amounts of macronutrients such as the amount of fats, protein, and sodium it contains. By utilizing some nutritional information in a recipe, I can estimate its total caloric content.

To evaluate the performance of the regression model, I will use the R-squared value. R-squared is a common and intuitive metric for measuring how well a regression model fits the data. It shows the proportion of the variance in the target variable (in this case, calories) that is explained by the model. The value of R-squared ranges from 0 to 1, where:
- A value close to 0 indicates that the model does not explain much of the variation in the target variable, meaning the model's predictions are not very accurate.
- A value close to 1 means the model does a great job of explaining the variance in the target variable, suggesting that the predictions are more accurate and reliable.
Thus, the goal is to achieve an R-squared value as close to 1 as possible, which would indicate that the model is effectively capturing the relationship between the nutritional information of a recipe and its caloric content. This will give me confidence that the model can make accurate predictions based on the given input data.

## Baseline Model: 
In this baseline model, I will use standard linear regression with three feature variables: `protein (grams)`, `total fat (grams)`, and `high_protein`. The quantitative variables that being used are protein and total fat. `high_protein` is a categorical variable where there are only True and False, to handle this issue One Hot Encoding will be required. One Hot Encoding will allow create categorical data into numeric, for example if`high_protein` is True it'll be 1, while False will be 0.

### Results of Baseline Model:
The model achieved a R-Squared value of 0.73 on the testing data, meaning it explains 73% of the variance in the calorie count. While this indicates a moderate fit, there is room for improvement. I belive that this model isn't the best it could be becauase there could be more variables that could influence the amount of calories in a recipe such as carbohydrates, sodium, and saturated fat. Another possible column we could investigate is the tags column which has tags like 'low-calorie' which could influence the prediction my model makes. Ovearll, with the little features in the linear regression model, it does an okay job. A possible idea for the final model is trying polynomial regression to see if there is realtationship between the features and calories that I overlooked.

## Final Model: 

## Fairness Analysis:
To evaluate the fairness of my model, I investigated potential bias in predictions based on recipe submission dates. Recipes were split into two groups: Older (submitted in 2013 or earlier) and Newer (submitted in 2014 or later), with 2013 as the cutoff since it is the median submission year. I assessed fairness using the Root Mean Squared Error (RMSE) as the metric, where lower RMSE indicates better model performance. For example, an RMSE of 200 implies that predictions, on average, deviate by 200 calories from the actual values.

**Null Hypothesis:** The model is fair; any difference in RMSE between the two groups is due to random chance.
**Alternative Hypothesis:** The model is unfair; it predicts calories more accurately for newer recipes than for older recipes.
**Test Statistic:** Difference in RMSE between the two groups.
**Significance Level:** 0.05


<iframe
src='Graphs/fairness_test.html'
width='800'
height='600'
frameborder="0"
></iframe>


**Analysis:** I added a relevance column to label recipes as "newer" or "older" and calculated the observed test statistic by grouping recipes by relevance and finding each group's RMSE using my model's predictions. I then performed 5,000 permutations by shuffling the relevance labels and recalculating the RMSE difference for each shuffle. This allowed me to construct a distribution of the test statistic under the null hypothesis.

The observed p-value was 0.02, which is below the significance level of 0.05. This means I **reject the null hypothesis**, concluding that my model predicts calories for newer recipes more accurately than for older recipes. This suggests a potential bias in my model's performance.










