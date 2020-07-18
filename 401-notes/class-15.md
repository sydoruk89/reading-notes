# Machine Learning
## There are 5 core steps:
1. Exploratory Analysis
First, "get to know" the data. This step should be quick, efficient, and decisive.
2. Data Cleaning
Then, clean your data to avoid many common pitfalls. Better data beats fancier algorithms.
3. Feature Engineering
Next, help your algorithms "focus" on what's important by creating new features.
4. Algorithm Selection
Choose the best, most appropriate algorithms without wasting your time.
5. Model Training
- Finally, train your models. This step is pretty formulaic once you've done

## Algorithm Selection
## Regularized Regression Algos
Regularization is a technique used to prevent overfitting by artificially penalizing model coefficients.
- It can discourage large coefficients (by dampening them).
- It can also remove features entirely (by setting their coefficients to 0).
- The "strength" of the penalty is tunable. (More on this tomorrow...)

## Lasso Regression
Lasso, or LASSO, stands for Least Absolute Shrinkage and Selection Operator.
- Lasso regression penalizes the absolute size of coefficients.
- Practically, this leads to coefficients that can be exactly 0.
- Thus, Lasso offers automatic feature selection because it can completely remove some features.
- Remember, the "strength" of the penalty should be tuned.
- A stronger penalty leads to more coefficients pushed to zero.

## Ridge Regression
Ridge stands Really Intense Dangerous Grapefruit Eating (just kidding... it's just ridge).
- Ridge regression penalizes the squared size of coefficients.
- Practically, this leads to smaller coefficients, but it doesn't force them to 0.
- In other words, Ridge offers feature shrinkage.
- Again, the "strength" of the penalty should be tuned.
- A stronger penalty leads to coefficients pushed closer to zero.

## Elastic-Net
Elastic-Net is a compromise between Lasso and Ridge.
- Elastic-Net penalizes a mix of both absolute and squared size.
- The ratio of the two penalty types should be tuned.
- The overall strength should also be tuned.
Oh and in case you’re wondering, there’s no "best" type of penalty. It really depends on the dataset and the problem. We recommend trying different algorithms that use a range of penalty strengths as part of the tuning process, which we'll cover in detail tomorrow.

## Random forests
Random forests train a large number of "strong" decision trees and combine their predictions through bagging.
1. In addition, there are two sources of "randomness" for random forests:
2. Each tree is only allowed to choose from a random subset of features to split on (leading to feature selection).
Each tree is only trained on a random subset of observations (a process called resampling).
In practice, random forests tend to perform very well right out of the box.
* They often beat many other models that take up to weeks to develop.
* They are the perfect "swiss-army-knife" algorithm that almost always gets good results.
* They don’t have many complicated parameters to tune.

## Boosted trees
Boosted trees train a sequence of "weak", constrained decision trees and combine their predictions through boosting.
- Each tree is allowed a maximum depth, which should be tuned.
- Each tree in the sequence tries to correct the prediction errors of the one before it.
In practice, boosted trees tend to have the highest performance ceilings.
- They often beat many other types of models after proper tuning.
- They are more complicated to tune than random forests.

## Python Data Wrangling Sample
1. Import libraries and dataset
import pandas as pd
pd.options.display.float_format = '{:,.2f}'.format
pd.options.display.max_rows = 200
pd.options.display.max_columns = 100
 
df = pd.read_csv('BNC2_sample.csv',
                 names=['Code', 'Date', 'Open', 'High', 'Low', 
                        'Close', 'Volume', 'VWAP', 'TWAP'])
 
2. Filter unwanted observations
gwa_codes = [code for code in df.Code.unique() if 'GWA_' in code]
df = df[df.Code.isin(gwa_codes)]
 
3. Pivot the dataset
pivoted_df = df.pivot(index='Date', columns='Code', values='VWAP')
 
4. Shift the pivoted dataset
delta_dict = {}
for offset in [7, 14, 21, 28]:
    delta_dict['delta_{}'.format(offset)] = pivoted_df / pivoted_df.shift(offset) - 1
    
5. Melt the shifted dataset
melted_dfs = []
for key, delta_df in delta_dict.items():
    melted_dfs.append( delta_df.reset_index().melt(id_vars=['Date'], value_name=key) )
 
return_df = pivoted_df.shift(-7) / pivoted_df - 1.0
melted_dfs.append( return_df.reset_index().melt(id_vars=['Date'], value_name='return_7') )
 
6. Reduce-merge the melted data
from functools import reduce
 
base_df = df[['Date', 'Code', 'Volume', 'VWAP']]
feature_dfs = [base_df] + melted_dfs
 
abt = reduce(lambda left,right: pd.merge(left,right,on=['Date', 'Code']), feature_dfs)
 
7. Aggregate with group-by.
abt['month'] = abt.Date.apply(lambda x: x[:7])
gb_df = abt.groupby(['Code', 'month']).first().reset_index()
