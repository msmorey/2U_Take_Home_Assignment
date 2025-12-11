# Statistics for Data Science: Stats In Python

## Introduction

Now that you understand measures of central tendency, we're ready to take our understanding of stats to the next level. Many data projects draw conclusions but fall short of mathematically defending those conclusions. In this section, we'll explore the statistical tools available in Python that can help separate valuable insights from the noise. 

## Learning Objectives

In this lesson, you will:

* Use the SciPy library.

* Perform hypothesis testing using t-tests.

* Understand the difference between `ttest_ind` and `ttest_ind_from_stats`.

* Interpret p-values.

## The SciPy Module

The SciPy library contains a multitude of functions useful in scientific applications. In this section, we'll be using the statistical portion of the library.

### Installation

Google Colab has the `scipy` library preinstalled, so if you're using Colab you shouldn't need to install anything for this lesson. If you're using another coding environment, you may have to run the following command to install the `scipy` library:

```txt
pip install scipy
```

### Statistical Significance: The p-value

Imagine that sales were \$10k in March, \$9k in April, and $15k in March. Did something meaningful happen in March, or did that change happen by random chance? How can we quantify whether a conclusion is meaningful or just noise? Well, statistics has an answer for this: the *p-value*.

> A *p-value* is the probability of a specific result occurring by random chance.

This means that if we run a statistical test on our results, **a lower p-value indicates a higher likelihood that our results are meaningful.**

> When the p-value from a test is below a pre-chosen threshold, the result is said to be *statistically significant*.

In many research applications, statistical significance occurs a `p < 0.05`, though this threshold should be chosen for each project depending on risk tolerance. For a project where less risk is acceptable, setting a lower p-value may be appropriate. A higher threshold may be appropriate in applications that can tolerate more risk.

### The t-test

One of the most common statistics used in business is the **mean**. A *t-test* helps us statistically compare means.

> A *t-test* compares two means to determine whether the difference between them is statistically significant.

For example, if we measure the speed of cars on a road and find that red cars drive an average of 5mph over the speed limit and blue cars drive an average of 1mph under the speed limit, the t-test can help us determine whether the difference in results is statistically significant or more likely to be due to random chance. There are three main types of t-test: independent sample t-tests, paired samples t-tests, and one-sample t-tests. 

#### Independent Samples t-test

When working with data, often we are interested not just in the data as a whole but in comparing across groups within the data. Take tennis for example: it would be interesting to know what the average serve speed was in a professional tournament, but what if we wanted to compare right hand serves to left hand serves? The *independent samples t-test* is the perfect tool for the job!

> An *independent samples t-test* compares the means of two separate samples.

To perform this test, the means, standard deviations, and total number of observations for each group are needed. The formula can be simplified to this interpretation: 

```txt
t = (difference between group means​) / (variability of groups)
```

The `scipy` module gives us access to two ways to perform this test. You can calculate the means, standard deviations, and total number of rows for each group and enter them into the `ttest_ind_from_stats` function:

```python
from scipy import stats

t_value, p_value = stats.ttest_ind_from_stats(mean_A, std_A, count_A, mean_B, std_B, count_B)
```

Or you can use the more automated `ttest_ind` on Pandas Series. This version calculates the means, standard deviations, and row counts for you:

```python
from scipy import stats

t_value, p_value = stats.ttest_ind(series_A, series_B)
```

Both methods will produce a verbose result that includes a p-value.

Lets try this out for ourselves!

#### CODE: Tennis Serves

In this coding activity, you'll learn how to compare two sample means from the same population using an independent samples t-test.

**NOTE: Always choose your p-value before starting any testing! For this activity we'll use `p < 0.05`. 

1. Start by opening Google Colab or your preferred coding environment.

2. Add the following library imports:

```python
import pandas as pd
from scipy import stats
```

2. Import the `tennis_serves.csv` file into a Pandas DataFrame.

3. Create a variable that holds a DataFrame with only the right handed serves.

4. Create a variable that holds a DataFrame with only the left handed serves.

5. Calculate and store the mean, standard deviation, and number of observations of the left handed serves in separate variables.

6. Calculate and store the mean, standard deviation, and number of observations of the right handed serves in separate variables.

7. Print the values for both hands using f-strings.

8. Use the following code with your own variables to compare the two means with an independent samples t-test:

```python
t_stat, p_value = stats.ttest_ind_from_stats(rh_mean, rh_std, rh_obs, lh_mean, lh_std, lh_obs)
print(p_value)
```

9. Try using the more automated `ttest_ind` function by using the following code with your own variables:

```python
t_stat, p_value = stats.ttest_ind(rh_serves_df['Serve Speed'], lh_serves_df['Serve Speed'])
print(p_value)
```

10. Interpret the p-value to determine whether the differences are statistically significant.

#### REVIEW: Tennis Serves

To begin, import all the necessary libraries including the `stats` portion of the `scipy` library.

```python
import pandas as pd
from scipy import stats
```

To compare left and right handed serves, we first need to separate out the left handed data from the right handed data using `loc`.

```python
rh_serves_df = serves_df.loc[serves_df['Hand'] == 'R']
lh_serves_df = serves_df.loc[serves_df['Hand'] == 'L']
```

For the `ttest_ind_from_stats` function, we'll need to calculate the mean, standard deviation, and row count of each DataFrame ourselves. We'll use the built in Pandas functions, but Numpy could also be used.

```python
# Calculate mean, std, and row count for right handed serves.
rh_mean = rh_serves_df['Serve Speed'].mean()
rh_std = rh_serves_df['Serve Speed'].std()
rh_obs = rh_serves_df['Serve Speed'].count()

# Calculate mean, std, and row count for left handed serves.
lh_mean = lh_serves_df["Serve Speed"].mean()
lh_std = lh_serves_df["Serve Speed"].std()
lh_obs = lh_serves_df["Serve Speed"].count()

# Use f-strings to print the stats
print(f"The mean of right handed serves is {round(rh_mean, 2)} with a standard deviation of {round(rh_std, 2)} and {rh_obs} data points.")
print(f"The mean of left handed serves is {round(lh_mean, 2)} with a standard deviation of {round(lh_std, 2)} and {lh_obs} data points.")
```

The output of the print statements is below:

```text
The mean of right handed serves is 120.18 with a standard deviation of 6.11 and 11 data points.
The mean of left handed serves is 110.0 with a standard deviation of 5.29 and 8 data points.
```

The right handed serves are, on average, 10mph faster. Is that difference just due to our sample or is this a *statistically significant* difference between serves? We'll use the `ttest_ind_from_stats` to find out.

```python
t_stat, p_value = stats.ttest_ind_from_stats(rh_mean, rh_std, rh_obs, lh_mean, lh_std, lh_obs)
print(p_value)
```

The output of the print statement is below:

```text
0.0014768237288612883
```

Instead of calculating the means, standard deviations, and row counts ourselves, we could also have used `ttest_ind` to calculate the p-value directly from the left hand and right hand Pandas Series.

```python
t_stat, p_value = stats.ttest_ind(rh_serves_df['Serve Speed'], lh_serves_df['Serve Speed'])
print(p_value)
```

The output of the print statement is below:

```text
0.0014768237288612883
```

The results are the same, but note that by using `ttest_ind`, we don't learn what the means actually are. We are simply given the p-value.

Well the results are clear, with a p-value of 0.0015 (which is less than the threshold of 0.05) we can safely say that the difference in serve speed between right and left handed serves in this dataset is *statistically significant*. If you have any remaining questions, chat with your AI Assistant! For an extra challenge, try repeating these steps to compare serves on grass against serves on clay!