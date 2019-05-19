## Analyze A/B Test Results

You may either submit your notebook through the workspace here, or you may work from your local machine and submit through the next page.  Either way assure that your code passes the project [RUBRIC](https://review.udacity.com/#!/projects/37e27304-ad47-4eb0-a1ab-8c12f60e43d0/rubric).  **Please save regularly.**

This project will assure you have mastered the subjects covered in the statistics lessons.  The hope is to have this project be as comprehensive of these topics as possible.  Good luck!

## Table of Contents
- [Introduction](#intro)
- [Part I - Probability](#probability)
- [Part II - A/B Test](#ab_test)
- [Part III - Regression](#regression)


<a id='intro'></a>
### Introduction

A/B tests are very commonly performed by data analysts and data scientists.  It is important that you get some practice working with the difficulties of these 

For this project, you will be working to understand the results of an A/B test run by an e-commerce website.  Your goal is to work through this notebook to help the company understand if they should implement the new page, keep the old page, or perhaps run the experiment longer to make their decision.

**As you work through this notebook, follow along in the classroom and answer the corresponding quiz questions associated with each question.** The labels for each classroom concept are provided for each question.  This will assure you are on the right track as you work through the project, and you can feel more confident in your final submission meeting the criteria.  As a final check, assure you meet all the criteria on the [RUBRIC](https://review.udacity.com/#!/projects/37e27304-ad47-4eb0-a1ab-8c12f60e43d0/rubric).

<a id='probability'></a>
#### Part I - Probability

To get started, let's import our libraries.

```python
import pandas as pd
import numpy as np
import random
import matplotlib.pyplot as plt
%matplotlib inline
#We are setting the seed to assure you get the same answers on quizzes as we set up
random.seed(42)
```

`1.` Now, read in the `ab_data.csv` data. Store it in `df`.  **Use your dataframe to answer the questions in Quiz 1 of the classroom.**

a. Read in the dataset and take a look at the top few rows here:
```python
df = pd.read_csv('ab_data.csv')
```
b. Use the cell below to find the number of rows in the dataset.
```python
df.shape[0] #294478
```
c. The number of unique users in the dataset.
```python
df.user_id.nunique() #290584
```
d. The proportion of users converted.
```python
(df.converted == 1).mean() #0.11965919355605512
```
e. The number of times the `new_page` and `treatment` don't match.
```python
t = df.query('landing_page == "old_page" and group == "treatment"').user_id.size
c = df.query('landing_page == "new_page" and group == "control"').user_id.size
t + c #3893
```
f. Do any of the rows have missing values?
```python
df.isnull().values.any() #False
```

`2.` For the rows where **treatment** does not match with **new_page** or **control** does not match with **old_page**, we cannot be sure if this row truly received the new or old page.  Use **Quiz 2** in the classroom to figure out how we should handle these rows.  

a. Now use the answer to the quiz to create a new dataset that meets the specifications from the quiz.  Store your new dataframe in **df2**.
```python
df_1 = df.query('landing_page == "old_page" and group == "control"') 
df_2 = df.query('landing_page == "new_page" and group == "treatment"')
df2 = df_1.append(df_2, ignore_index=True)
```
```python
# Double Check all of the correct rows were removed - this should be 0
df2[((df2['group'] == 'treatment') == (df2['landing_page'] == 'new_page')) == False].shape[0] #0
```
`3.` Use **df2** and the cells below to answer questions for **Quiz3** in the classroom.
a. How many unique **user_id**s are in **df2**?
```python
df2.user_id.nunique() #290584
```
b. There is one **user_id** repeated in **df2**.  What is it?
```python
df2[df2.user_id.duplicated()]
```
c. What is the row information for the repeat **user_id**? 
```python
df2.iloc[146678]
```
d. Remove **one** of the rows with a duplicate **user_id**, but keep your dataframe as **df2**.
```python
df2.drop(146678,inplace=True)
```
`4.` Use **df2** in the cells below to answer the quiz questions related to **Quiz 4** in the classroom.

a. What is the probability of an individual converting regardless of the page they receive?
```python
(df2.converted == 1).mean() #0.11959708724499628
```
b. Given that an individual was in the `control` group, what is the probability they converted?
```python
obs_control = (df2.query('group == "control"')['converted'] == 1).mean()
obs_control #0.1203863045004612
```
c. Given that an individual was in the `treatment` group, what is the probability they converted?
```python
obs_treatment = (df2.query('group == "treatment"')['converted'] == 1).mean()
obs_treatment #0.11880806551510564
```
d. What is the probability that an individual received the new page?
```python
(df2.landing_page == "new_page").mean() #0.50006194422266881
```
```python
obs_treatment - obs_control #-0.0015782389853555567
``` 
e. Consider your results from parts (a) through (d) above, and explain below whether you think there is sufficient evidence to conclude that the new treatment page leads to more conversions.

The difference is very smaller and from the observed data difference, it does not seem like there is sufficient evidence to conclude that new treatment page leads to more conversions.