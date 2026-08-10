## Introduction

Imagine you are applying for a position as data analyst at the International Olympic Committee and you have been asked to create a report about the Olympic Games.\
You receive the dataset as an excel file, that you need to analyse to create a report titled "Things you did not know about the Olympic Games".

![](https://d7whxh71cqykp.cloudfront.net/uploads/image/data/2277/04-project-olympics.png)

## The data

The data is in a spreadsheet format and it is split into four sheets.

-   **games**: basic data about the games (season, city...)
-   **athletes**: contains basic data about the athletes (name, height, weight, gender...) that are constant for all games. Note that we only have one measure of weight, height and gender so you will have to assume that this did not change even if the athlete participated to the Games more than once
-   **country**: contains basic data about the athletes that sometimes changed over time (country, age)
-   **medals**: a list of results (type of medals for the winners, `NA` for the others)

**Hint**: Before you start doing lots of `joins`, check what happened at the 1956 games, it might be interesting...


## Loading and exploring the data

Start by loading the data and by performing some exploratory data analysis (check unique values of the characters column, ranges of numerical columns, duplicates etc).


## Data analysis

### Athletes info

#### Part 1

Have some athletes competed for different countries over time?

Use inline code to print the result and comment your findings. 
How would you explain these results?

#### Part 2

Who are the ten athletes that took part in most games? 

#### Part 3

Who are the ten athletes that competed in the most events? Did some athletes take part in more than one event during a game?

### Countries info

#### Part 4

Create a new table showing the number of medals per country (rows) and per year (column).
Keep only the 15 countries with the most medals overall.

**Hint**: First, find the top 15 countries, so you can use it to filter the table.
Your final table should have 15 rows (one row per country), and each column should correspond to a year.

#### Part 5

Is there a relationship between country and the probability of winning a medal?

We know that some countries win more medals than others.
In this part we want to work out whether some countries win more medals simply because they have more athletes competing for more events, or whether their athletes are in fact performing better in general.

For this part, look only at the top 15 countries that you established in part 6.

- Create a horizontal barchart listing for each of the fifteen countries the percentage of medals won *out of all medals competed for by that country*. 

**Hint**: To calculate the total number of medals competed for, you should count all the times a country won a medal plus all the time the same country did not win (rows in which the `medal` column is `NA`)

- Looking at the chart, what do you think: is there a relationship between country and the probability of winning a medal? Comment your findings.

- Run a Chi Square test to test the null-hypothesis: "There is no relationship between country and whether they win a medal or not".

- Include in your report a description of the interpretation of the results. Does it match your expectation based on the bar chart?

### Athletes and Sports

#### Part 6

Create a scatterplot showing the average height versus the average weight of competitors per sport (one dot per sport).
Add labels with the sport names for:

-   the largest average height
-   the largest average weight
-   the smallest average height
-   the smallest average weight
-   the largest average BMI
-   the smallest average BMI

It might be that the same dot qualifies for multiple labels.
The formula to calculate Body Mass Index (BMI) is:

```         
weight in kg / (height in meters)^2
```

**Hint**: Take advantage of `ggplot2` layering. A "clean" way to create such a plot would be to use two tibbles: one that includes all the different sports and one that includes only the sports corresponding to your criteria (max average... etc). You can then use two `geom_` layers to add all the information in the plot.

### Part 7

Create a line plot showing the number of medals given by year (one line for Gold, one line for Silver and one line for Bronze).
Does it change over time?
Use facets to separate the medals at Summer games and Winter games.

Comment the results below the plot.
