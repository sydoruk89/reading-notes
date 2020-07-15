# Seaborn
## Emphasizing continuity with line plots
Scatter plots are highly effective, but there is no universally optimal type of visualisation. Instead, the visual representation should be adapted for the specifics of the dataset and to the question you are trying to answer with the plot.

With some datasets, you may want to understand changes in one variable as a function of time, or a similarly continuous variable. In this situation, a good choice is to draw a line plot. In seaborn, this can be accomplished by the lineplot() function, either directly or with relplot() by setting kind="line":

df = pd.DataFrame(dict(time=np.arange(500),
                       value=np.random.randn(500).cumsum()))
g = sns.relplot(x="time", y="value", kind="line", data=df)
g.fig.autofmt_xdate()

## Aggregation and representing uncertainty
More complex datasets will have multiple measurements for the same value of the x variable. The default behavior in seaborn is to aggregate the multiple measurements at each x value by plotting the mean and the 95% confidence interval around the mean:

fmri = sns.load_dataset("fmri")
sns.relplot(x="timepoint", y="signal", kind="line", data=fmri);

The confidence intervals are computed using bootstrapping, which can be time-intensive for larger datasets. It’s therefore possible to disable them:

sns.relplot(x="timepoint", y="signal", ci=None, kind="line", data=fmri);

## Plotting subsets of data with semantic mappings
The lineplot() function has the same flexibility as scatterplot(): it can show up to three additional variables by modifying the hue, size, and style of the plot elements. It does so using the same API as scatterplot(), meaning that we don’t need to stop and think about the parameters that control the look of lines vs. points in matplotlib.

Using semantics in lineplot() will also determine how the data get aggregated. For example, adding a hue semantic with two levels splits the plot into two lines and error bands, coloring each to indicate which subset of the data they correspond to.

sns.relplot(x="timepoint", y="signal", hue="event", kind="line", data=fmri);

Adding a style semantic to a line plot changes the pattern of dashes in the line by default:

sns.relplot(x="timepoint", y="signal", hue="region", style="event",
            kind="line", data=fmri);

But you can identify subsets by the markers used at each observation, either together with the dashes or instead of them:

sns.relplot(x="timepoint", y="signal", hue="region", style="event",
            dashes=False, markers=True, kind="line", data=fmri);

## Plotting with categorical data

Categorical scatterplots:

stripplot() (with kind="strip"; the default)

swarmplot() (with kind="swarm")

Categorical distribution plots:

boxplot() (with kind="box")

violinplot() (with kind="violin")

boxenplot() (with kind="boxen")

Categorical estimate plots:

pointplot() (with kind="point")

barplot() (with kind="bar")

countplot() (with kind="count")

import seaborn as sns
import matplotlib.pyplot as plt
sns.set(style="ticks", color_codes=True)

# Categorical scatterplots
tips = sns.load_dataset("tips")
sns.catplot(x="day", y="total_bill", data=tips);

The jitter parameter controls the magnitude of jitter or disables it altogether:

sns.catplot(x="day", y="total_bill", jitter=False, data=tips);

The second approach adjusts the points along the categorical axis using an algorithm that prevents them from overlapping. It can give a better representation of the distribution of observations, although it only works well for relatively small datasets. This kind of plot is sometimes called a “beeswarm” and is drawn in seaborn by swarmplot(), which is activated by setting kind="swarm" in catplot():

sns.catplot(x="day", y="total_bill", kind="swarm", data=tips);

## Bar plots
A familiar style of plot that accomplishes this goal is a bar plot. In seaborn, the barplot() function operates on a full dataset and applies a function to obtain the estimate (taking the mean by default). When there are multiple observations in each category, it also uses bootstrapping to compute a confidence interval around the estimate, which is plotted using error bars:

titanic = sns.load_dataset("titanic")
sns.catplot(x="sex", y="survived", hue="class", kind="bar", data=titanic);

## Visualizing the distribution of a dataset

import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
from scipy import stats
sns.set(color_codes=True)

## Plotting univariate distributions
x = np.random.normal(size=100)
sns.distplot(x);

sns.distplot(x, kde=False, rug=True);

## Conditional small multiples
import seaborn as sns
import matplotlib.pyplot as plt
sns.set(style="ticks")

tips = sns.load_dataset("tips")
g = sns.FacetGrid(tips, col="time")

g = sns.FacetGrid(tips, col="time")
g.map(plt.hist, "tip");

# Bokeh
import numpy as np

from bokeh.io import output_notebook, show
from bokeh.plotting import figure

- create a new plot with default tools, using figure
p = figure(plot_width=400, plot_height=400)

- add a circle renderer with x and y coordinates, size, color, and alpha
p.circle([1, 2, 3, 4, 5], [6, 7, 2, 4, 5], size=15, line_color="navy", fill_color="orange", fill_alpha=0.5)

show(p) # show the results