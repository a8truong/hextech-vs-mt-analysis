# Hextech vs. Mountains: Which is the better dragon?
## Introduction

The dataset used for this analysis was of League of Legends competitive matches in 2022 from [Oracle’s Elixer](https://oracleselixir.com/tools/downloads), it has 149232 rows and 123 columns. The data details the matches, including but not limited to the league name, if they won, and the dragons killed by each team in that match. The columns we will be using are: the league name, which is the league the match was played in, result, which is whether or not that team won that match, and infernals, mountains, clouds, oceans, and hextechs- all of which are the number of dragons that team killed in that match. Using this data, we will determine: are hextech dragons more relevant to the match result than mountain dragons? This is important for analysis of the league of legends meta game, such as making the decision if it is worth risking a team fight to try and get a dragon. It is also relevant to future balance decisions, specifically when deciding if specific dragons need a buff or nerf.

## Cleaning and EDA

### Data Cleaning

Since we are not looking at individual performance, the first thing we did to clean our data was to only take in the rows where playername is null, so that our dataframe only has the summary rows for each match. Then, we only filtered out columns we did not need, so that we end up with only columns about the dragons killed and the types, the result of the match, and the league name. We also added a column that stated whether or not a match has at least one kill for hextech dragons, and another for mountain dragons. 

```py
| result   |   mountains |   hextechs | has_mountain   | has_hextech   |
|:---------|------------:|-----------:|:---------------|:--------------|
| False    |           0 |          1 | False          | True          |
| True     |           1 |          0 | True           | False         |
| False    |           1 |          0 | True           | False         |
| True     |           2 |          1 | True           | True          |
| True     |         nan |        nan | False          | False         |
```

### Univariate Analysis
<iframe src="assets/total_num_of_hex_plot.html" width=800 height=600 frameBorder=0></iframe>
The histogram above shows the distribution of hextech dragon kills in a match. With 0 hextech kills being the most common value and more kills being less common, we can assume that the likelihood of a hextech dragon appearing in a match is relatively low. 

<iframe src="assets/total_num_of_mountain_plot.html" width=800 height=600 frameBorder=0></iframe>
The histogram above shows the distribution of mountain dragon kills in a match. With 0 mountain kills being the most common value and more kills being less common, similarly to hextech, we can assume that the likelihood of a mountain dragon appearing in a match is also relatively low. 

### Bivariate Analysis
<iframe src="assets/win_by_num_of_hextech_plot.html" width=800 height=600 frameBorder=0></iframe>
Our histogram here displays the relationship between the number of hextech dragon kills and the win-loss ratio for each kill count. As the number of kills goes up, we can see that there are more wins than losses. 

<iframe src="assets/win_by_num_of_mountain_plot.html" width=800 height=600 frameBorder=0></iframe>
Our histogram here displays the relationship between the number of mountain dragon kills and the win-loss ratio for each kill count. As the number of kills goes up, we can see that there are more wins than losses, similar to hextech dragon kills. 

### Interesting Aggregates

```py
'| result   |   infernals |   mountains |   clouds |   oceans |   hextechs |   dragons |   opp_dragons |   has_mountain |   has_hextech |
|:---------|------------:|------------:|---------:|---------:|-----------:|----------:|--------------:|---------------:|--------------:|
| False    |    0.280064 |    0.285997 | 0.288351 | 0.283454 |   0.272057 |   1.43777 |       3.03152 |       0.211449 |      0.200917 |
| True     |    0.595478 |    0.579463 | 0.581159 | 0.581818 |   0.586889 |   3.03209 |       1.43783 |       0.360302 |      0.360946 |'
```

When we group by the result of the match and find the mean, we can see that wins have a higher average for all dragon type kills, and the average of opposite team dragon kills are lower. This shows that in general, getting dragon kills increases the likelihood of winning a match. 

## Assessment of Missingness

### NMAR Analysis

We do not believe that there is a column in our dataset that is NMAR. This is because our missing data was entirely in the dragon kills columns- specifically the columns for the kills of each dragon type. These missing rows are notably missing for specific leagues, so we can reasonably predict the missingness of this data based on the league column, effectively making these columns MAR. 

### Missingness Dependency

When we did permutation testing to see if the missingess of hextech kills were dependent on the results column, we received a p-value of 1.0, meaning that it is extremely unlikely that the missingness of hextech kills are dependent on results. 

When we did permutation testing to see if the missingness of hextech kills were depending on the results column, we received a p-value of 0.0, meaning that it is extremely likely that the missingness of hextech kills are dependent on the league. 

## Hypothesis Testing

Our null hypothesis was that having at least one hextech dragon kill or at least one mountain dragon kill equally increases the odds of winning. Our alternative hypothesis was that having at least one hextech dragon increases the odds of winning more than having at least one mountain dragon kill. Our test statistic was the difference of means of the results of matches that had at least one hextech dragon kill and matches that had at least one mountain dragon kill. This was a good choice for answering our question because we were effectively comparing the win ratios between dragon types. Our resulting p-value was 0.0383, which is below the .05 significance level. With this, we reject the null hypothesis that the influence of dragon kills are the same for both types, and conclude that hextech dragon kills are likely to increase the odds of winning more than mountain dragon kills.
