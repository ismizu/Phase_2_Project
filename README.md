# King County Housing EDA and Modeling

## Overview

In this project I conduct data analysis on King County Housing data for the purpose of making predictions via Linear Regression. In addition to familiarizing myself with potential modeling problems, I also create features in an attempt to better represent housing prices in my model.

![pexels-photo-1546166.jpeg](https://raw.githubusercontent.com/ismizu/Phase_2_Project/main/images/pexels-photo-1546166.jpeg)

<center>
    Image by
    <a href = https://www.pexels.com/@davidmcbee>David McBee</a>
    from
    <a href = https://www.pexels.com>Pexels</a>
</center>

## Data Structure

Data csv's can be found in the /data folder.
- kc_house_data_test_features and kc_data_train obtained via Flatiron School
- All csv's prefixed with "king_county_" obtained from [PolicyMap](https://www.policymap.com)

Notebooks can be found in the /notebooks folder
- king_county_eda contains all information regarding the data analysis and modeling processes
- model_predictions contains information regarding the model's usage on predictions
- the /pickle folder contains exported data from king_county_eda for use in model_predictions

Additional files:
- /model_predictions contains the csv with the model's price predictions
- /images contains all visualizations used

Initial observations presentation
- [Slide Deck](https://docs.google.com/presentation/d/1QEytX4pZJEd4TIHU9B-NSurKQgvzWxwPkQBihNJvBbk/edit?usp=sharing) regarding a brief view of initial observations

## Primary Focus

My primary focus for this project is three-fold:
1. Identify extreme values that may cause problems within my model.
2. Identify variables with very low value counts that may benefit from grouping into ranges
3. Engineer new features that might better represent homes that fall outside the average range
4. Place these features within a model to predict house prices


# <center> Data Analysis </center>


After viewing the initial data, I began my data analysis with the few that had the highest correlation with price.

![correlation_matrix_high_correlation.png](https://raw.githubusercontent.com/ismizu/Phase_2_Project/main/images/correlation_matrix_high_correlation.png)

I began by looking into bedrooms and immediately detected an irregularity. A quick plot showing the number of bedrooms per house shows that one value in particular sticks out.

![bedrooms_per_house.png](https://raw.githubusercontent.com/ismizu/Phase_2_Project/main/images/bedrooms_per_house.png)


At an initial glance, there is no particular aspect that stands out as far as explaining the bedrooms count.

A quick plug of the latitude and longitude show us the house's location.


![33_bedrooms_house_folium.png](https://raw.githubusercontent.com/ismizu/Phase_2_Project/main/images/33_bedrooms_house_folium.png)

It would appear that the houses in this area would be incapable of supporting thirty-three average sized bedrooms, or smaller than average for that matter. As such, I believe it is safe to make the assumption that thirty-three was likely made in error. I utilized the following parameter to rectify this issue:

- Set the max bedrooms to be approximately 5 standard deviations from the next largest value.
- If over this amount, set to the average number of bedrooms rounded to the nearest whole number


#  <center> Binning </center>


Following this, I investigated the grades column and found a suprising disparity in values.

![grades.png](https://raw.githubusercontent.com/ismizu/Phase_2_Project/main/images/grades.png)

A quick look found that 1-3 is below construction standards, 7 is average, and 11-13 is exceptional.

I took a look at the price disparity of the grades to get a better idea of how to bin these values, in order to account for the value count disparity.

![grade_distribution.png](https://raw.githubusercontent.com/ismizu/Phase_2_Project/main/images/grade_distribution.png)

As a compromise between: the grading's system, price disparity between them, as well as value counts; the following bins were selected for the data:

- Below Average Grade: All values below 7
- Average Grade: All values marked 7
- Above Average Grade: All values between 8 and 10
- High Grade: All values 11 and up

To double-check the statistical significance of this split, I ran an ANOVA test on each bin and found a p-value of 0, showing an extremely low chance that the price ranges in these bins are identical.

I then underwent an identical process in order to bin the bathroom values, viewing the same elements in order to come a conclusion regarding which values to group together.

![bathrooms_x_price.png](https://raw.githubusercontent.com/ismizu/Phase_2_Project/main/images/bathrooms_x_price.png)

To compromise in a similar manner to grade, the bathrooms were separated into the following bins:

- More than 5.5 bathrooms
- Between 3.75 and 5.5 bathrooms
- Between 2.75 and 3.75 bathrooms
- Between 1.25 and 2.75 bathrooms
- Less than 1.25 bathrooms

Running an ANOVA test on these variables shows a similarly low p-value.


# Accounting for High Value Homes


One of the items that threw off my initial model, was a much higher heteroskedasticity than showed by the final model. In order account for this, I plotted them against a map to view their locations.

![high_value_home_map.png](https://raw.githubusercontent.com/ismizu/Phase_2_Project/main/images/high_value_home_map.png)

Something that immediately pops out is their vicinity to water, as such I plotted additional points to try and capture a portion of these homes, utilizing the waterfront category as a primary marker.

![waterfront_wealth_map.png](https://raw.githubusercontent.com/ismizu/Phase_2_Project/main/images/waterfront_wealth_map.png)

Comparing these two maps, I can see that a good portion of the waterfront properties listed as above a $2 million price have been captured. As such, I created a variable to contain these.

I also checked its correlation with price as well as run a T-test between houses marked as waterfront_wealth and houses not marked so. The results showed a slight correlation as well as a very low p-value.


# Linear Regression Model


I now place them into a Linear Regression Model to view the model's R-squared value as a preliminary deciding factor.

With a satisfactory result, I continue to predicting results.

![ols_results.png](https://raw.githubusercontent.com/ismizu/Phase_2_Project/main/images/ols_results.png)


# <center> Testing the Model </center>


After the data analysis, I split the data into a test/train split to see how well it performs against unseen data.

The resulting RMSE is as follows:
- Training set RMSE: 140121.83
- Testing set RMSE: 134526.512

The resulting model has produced a result that is not bad. However, I did want to check how the residuals appear.

![residuals_check.png](https://raw.githubusercontent.com/ismizu/Phase_2_Project/main/images/residuals_check.png)

While the residuals produced a normal distribution, there was quite a bit of heteroskedasticity.

Unfortunately, additional methods produced similar results as well as greater variance. Thus, the initial model was selected for predictions.

# Summary

- Checking for extreme values revealed a likely error that was able to be cleaned
- Variance in column value count's were accounted for by binning different values into groups based on various factors
- High-value homes, the cause of heteroskedasticity in my model were accounted for by narrowing down their location
