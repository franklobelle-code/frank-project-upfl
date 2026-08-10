## Introduction

In this project, you are going to scrape your dataset from the website of a fake rental agency, using the `{rvest}` package, and then create a data analysis report.

![](https://d7whxh71cqykp.cloudfront.net/uploads/image/data/2228/04-project-immoscrape.png)

## The data

The data doesn't come to you as a dataset, it comes to you as a website.

-   <https://epfl-exts.github.io/rental-scrape/>

Your first challenge is to write the code needed to scrape the data contained in it.
To help you see what the end goal is, we placed a sample extract of the final dataset in your repository.
The file has only `5` rows and is called `sample_scraped_rental.csv`.

## Data import and cleaning

### Part 1

a- Get the full dataset out of the site.
You should end up with a `tibble` of 612 rows and 9 columns.

b- Explore the data.
Check the data type of each column and make sure you convert them to the right type, if needed.
Check the ranges for the numerical variables and the unique values for the character columns.
Write down any adjustment that you make.

**Warning**: During this exploratory step, make sure you do not lose any information.

## Exploratory data analysis

### Part 2

Create a scatterplot showing how price evolves with living space, for all the listed properties.

Add your interpretation below the plot.

### Part 3

Create a bar plot showing the number of properties by postcode.
Is the agency more "active" in certain areas?
Write down your comments.

### Part 4

Create a more complex scatterplot, showing how price evolves with living space by postcode and by floor.
You can use colors and/or facets to make the categories visible.

![](https://d7whxh71cqykp.cloudfront.net/uploads/image/data/4207/04-project-4-sketch.png)

Can you conclude anything from it?
Put your thoughts below your plot.
Don't overthink this: this is not so much about the quality of your analysis than checking that you can put a report together.

Integrate the most expensive and least expensive mean postcode/floor combo in your text with [inline code](https://rmarkdown.rstudio.com/lesson-4.html).

## Analysis

### Part 5

Can you see any trends for listings with addresses only available on demand?
Are they more expensive or less?
Bigger living space?
Higher floor?

**Hint**: Boxplot or violin plots work really well to compare distributions of values between groups.
Use a combination of the two to answer each of these questions and write down your comments.

**Warning**: Beware that both "on request" and "sur demande" denote this type of property.

### Part 6

Make a table summarising group size, median, average, standard-deviation, minimum and maximum of the variable *price per square-meter* (expressed in CHF/$m^2$), by address status (on request or available).

Have a look at the statistics.
What do you observe?
Is there a difference in price per square-meter for these two groups, and in what direction?

After writing down your initial remarks, state a statistical hypothesis and use a t-test to statistically compare the average price per square-meter for the two groups.

What is your conclusion, do the results of the test confirm your initial observations?

**Warning:** Look closely at the table before carrying the test.
Adjust anything in the data that could invalidate the results of the test and explain your reasoning.

### Part 7

Do the same as in part 6, but now using the variable *price* (expressed in CHF).

Compare the results of the analysis carried on the *price per square-meter* with those for the *price variables*.
Are there differences between the results?
Write down your interpretation and a general conclusion.

### Part 8

Convert a subset of 30 addresses to latitude and longitude using an API or `{tidygeocoder}` and plot them on a map using `{ggmap}` (or `{leaflet}` if you cannot get `{ggmap}` to work).

**Warning**: If you decide to use `{leaflet}`, please note that knitting to anything other than a `html_document` may lead to issues.
