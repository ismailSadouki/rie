Creating a figure with a grid of subplots is a very common task, so matplotlib
includes a convenience method, plt.subplots , that creates a new figure and returns
a NumPy array containing the created subplot objects:
	`fig, axes = plt.subplots(2, 3, sharex=True, sharey=True)`
change the spacing using the subplots_adjust method on Figure objects, also avail‐
able as a top-level function:
	`subplots_adjust(left=None, bottom=None, right=None, top=None,
	`wspace=None, hspace=None)`

To change the x-axis ticks, it’s easiest to use set_xticks and set_xticklabels .
	`ticks = ax.set_xticks([0, 250, 500, 750, 1000])`
	`labels = ax.set_xticklabels(['one', 'two', 'three', 'four', 'five'], rotation=30, fontsize='small')`
	Or by passing a dict
		`props = {
		``	'title': 'My first matplotlib plot',
		``	'xlabel': 'Stages'
			`}
		`ax.set(**props)`

### Annotations and Drawing on a Subplot
	`fig = plt.figure()
	`ax = fig.add_subplot(1, 1, 1)`
	`ax.text(x, y, 'Hello world!', family='monospace', fontsize=10)`
Annotations can draw both text and arrows arranged appropriately. (You can search about them)

### matplotlib Configuration
One way to modify the configuration programmatically from Python is to use the rc method;
	`plt.rc('figure', figsize=(10, 10))`
An easy way to write down the options in your program is as a dict:	
	`font_options = {'family' : 'monospace',
	``	'weight' : 'bold',
		`'size'
	``	: 'small'}
	`plt.rc('font', **font_options)`



## Plotting with pandas and seaborn
### Line Plots
DataFrame has a number of options allowing some flexibility with how the columns
are handled; for example, whether to plot them all on the same subplot or to create
separate subplots. See Table 9-4 for more on these.
![[Screenshot from 2024-08-13 22-16-21.png]]

### side-by-side bar chart
	`df = pd.DataFrame(np.random.rand(6, 4), index=['one', 'two', 'three', 'four', 'five', 'six'], columns=pd.Index(['A', 'B', 'C', 'D'], name='Genus'))
	`df.plot.bar()

	![[Screenshot from 2024-08-13 22-36-35.png]]
We create stacked bar plots from a DataFrame by passing stacked=True 
	`df.plot.barh(stacked=True, alpha=0.5)`
![[Screenshot from 2024-08-13 22-38-15.png]]
seaborn.barplot has a hue option that enables us to split by an additional categorical
value
	`sns.barplot(x='tip_pct', y='day', hue='time', data=tips, orient='h')`
![[Screenshot from 2024-08-13 22-45-34.png]]
Seaborn makes histograms and density plots  easier through its distplot method
	`sns.distplot(values, bins=100, color='k')`
![[Screenshot from 2024-08-13 23-11-10.png]]

In exploratory data analysis it’s helpful to be able to look at all the scatter plots among
a group of variables; this is known as a pairs plot or scatter plot matrix.
		`sns.pairplot(trans_data, diag_kind='kde', plot_kws={'alpha': 0.2})`
![[Screenshot from 2024-08-13 23-12-50.png]]



### Facet Grids and Categorical Data
one way to vis‐ualize data with many categorical variables is to use a facet grid.
	![[Screenshot from 2024-08-13 23-15-26.png]]`sns.factorplot(x='day', y='tip_pct', hue='time', col='smoker',kind='bar', data=tips[tips.tip_pct < 1])`
Instead of grouping by 'time' by different bar colors within a facet, we can also
expand the facet grid by adding one row per time value
	`sns.factorplot(x='day', y='tip_pct', row='time', col='smoker',kind='bar', data=tips[tips.tip_pct < 1])```
![[Screenshot from 2024-08-13 23-17-11 2.png]]
factorplot supports other plot types that may be useful depending on what you are
trying to display.
	`sns.factorplot(x='tip_pct', y='day', kind='box',data=tips[tips.tip_pct < 0.5])`
![[Screenshot from 2024-08-13 23-18-33 1.png]]