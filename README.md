# Investigating Proteins and their Relationship with Ratings and Calories

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
| `'user_id'`          | USER ID                                                       |
| `'recipe_id'`            | Recipe ID                                                 |
| `'date'`            | Date of interaction                                            |
| `'rating'`            | Rating given                                                 |
| `'review'`            | Review Text                                                  |


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
  
7. Changed the Protein to Grams:
   - This purpose of this transformation will be useful for my investigation. To do this, we first assume that a person is on 2,000 calorie diet, we take the Perent Daily Value of the recipe multiply it by 50 (the suggested ammount of protein for a 2,000 calorie diet) the divide it by 100 to express the current percentage as a fraction. 
  

### Univariate Analysis

In this analysis, I examined the distribution of the protein (in grams) in the recipes. In the plot below, one could determine that the distribution is skewed to the right. This suggests that most recipes on Food.com aren't extremely high in protein. WE could see that as we move more and more to the rigt the prescence of greater protein values is lessens

<iframe
src='Graphs/protein_dist.html'
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

In this analysis, I did an aggregate of calories/gram of protein and wanted to see its relationship with the number of steps a recipe has. First, I had to remove outliers in the number of steps with the IQR method (some recipes were outrageously long). I grouped by the number of steps each recipe took and did some aggregate to put in a chart and plotted the 


## Assessment of Missingness
In the data there seems to be three columns of interest that have a significant amount of missing values - those columns are `description`, `rating`, and `avg_rating`. In this section, I will investigate the missingness of values in some the columns that contain missing values.

### NMAR Analysis 
I belive that the missigness of the `rating` column is potentially Missing Not at Random. The reason for this is that perhaps that people perhaps didn't enjoy the recipes as much didn't want to leave a bad rating, and the inverse could be true is that people who did enjoy the recipe would rather leave a rating. This could be the chance as most of the average ratings in the dataset are 3 and up while there aren't many that are rated lower than 2.

### Missingness Dependency
Furthering the investigation of missingness, I moved on to examine missingness of `rating` by testing to see if there are any columns that would depend on it. In this part, I checked to see if `rating` column is dependent on the `calories (#)` column, which is the total number of calories in the recipe. First I setup the hypothesis test with following hypotheses: 
- **Null Hypothesis:**  The missigness does not depend on the calories column
- **Altenativate Hypothesis:** The missingness does depend on the calories column
- **Test Statistic** Absolue Difference of Means

  

<iframe
src='Graphs/missingness_test.html'
width='800'
height='600'
frameborder="0"
></iframe>


## Hypothesis Testing 




#### Conclusion of the Permutation Test


## Framing Prediction Problem


## Baseline Model 


## Final Model


## Fairness Analysis








