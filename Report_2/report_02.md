## Introduction

Here we will study open data about politicians in Zurich and use our knowledge to find patterns and learn some facts.

![](https://d7whxh71cqykp.cloudfront.net/uploads/image/data/2221/04-project-zh.png)

The data comes to you in a relational database, a functional `SQLite` database to be precise.

## The data

We placed in your repository a `.db` file:

-   `zh_politicians.db`

This file is an `SQLite` database and contains data about elected politicians in Zürich over the years. Some columns like `activity` are in German but this won't affect your analysis. The different tables have information about:

-   The elected persons (name, gender, year of birth, year of death...)
-   The mandates people have been elected for
-   The addresses of the elected persons
-   The affiliations to political organisations

Take the time to study the database structure, either directly with `R` using `{DBI}` and `{dplyr}` or first with the software we used in the course "DB Browser for SQLite".

## A Word of Warning

This dataset is **quite messy**: consider this is a real-life example! Missing dates, misspelled names...

Your goal is to do your best with what you were given and explain your thought process along the way. For example, if a politician seems to be 3 years old or 200 years old, filter him/her out before calculating the average age of politicians...

## Before you start: Nested Cells

Early on, you will realise that you need to create some "missing" data. You will need to use advanced concepts like nested cells. Although we strongly advise that you review the relevant units in the course, we also put a little example below.

> What if I create a column with `mutate()` that puts not one value in each cell, but a collection of values in each cell (`vector` or `list`)?

There is a dataset, called `starwars` that comes pre-installed with `{dplyr}` that will show you that kind of table. Try the code below:

``` r
library(dplyr)
starwars %>%
  select(name, species, films) # Showing only a few columns for clarity
```

```         
# A tibble: 87 x 3
   name               species films    
   <chr>              <chr>   <list>   
 1 Luke Skywalker     Human   <chr [5]>
 2 C-3PO              Droid   <chr [6]>
```

As you can see the cells in the column `films` do not contain "single values". Instead, they contain `vectors` of type `character`:

-   Row 1 has `5` items in the `films` column (probably meaning that "Luke Skywalker" was in 5 films)
-   Row 2 has `6` items in the `films` column (probably meaning that "C-3PO" was in 6 films)...

You can get a better look at the content of the film column if you use `View()`:

``` r
library(dplyr)
starwars %>%
  select(name, species, films) %>%
  View()
```

With `View()`, you will see at least the first items. For example the first row shows something like this:

```         
c("Revenge of the Sith", "Return of the Jedi",
  "The Empire Strikes Back", "A New Hope",
  "The Force Awakens")
```

We can see that the cell contains a `vector` indeed. Another way to see the content could be to extract the column out of the `tibble` with our trusted `pull()` function.

``` r
library(dplyr)
starwars %>%
  pull(films) %>%
  head(3) # showing just the first result...
```

```         
[[1]]
[1] "Revenge of the Sith"     "Return of the Jedi"      "The Empire Strikes Back"
[4] "A New Hope"              "The Force Awakens"      

[[2]]
[1] "Attack of the Clones"    "The Phantom Menace"      "Revenge of the Sith"    
[4] "Return of the Jedi"      "The Empire Strikes Back" "A New Hope"             

[[3]]
[1] "Attack of the Clones"    "The Phantom Menace"      "Revenge of the Sith"    
[4] "Return of the Jedi"      "The Empire Strikes Back" "A New Hope"             
[7] "The Force Awakens"
```

Here again, looking at these double brackets (`[[`) we see that we are dealing with a `list` with each item being a `vector` of films.

The `list-columns`, as they are called, are one of the most powerful features of the `{tidyverse}`: they kind of let you create "multi-dimensional" `tibbles`. But this only becomes interesting if they let you "come back" to a normal "flat" `tibble`...which is what the `unnest()` function from `{tidyr}` is used for!

Let's say that I want to calculate the number of characters that have been in each movie. For that I need to be able to use `group_by()` on a *per-film* basis. How can I get a column that will contains only one `film`? `unnest()` to the rescue!

```         
library(dplyr)
library(tidyr)
starwars %>%
  select(name, species, films) %>%
  unnest(films)
```

```         
# A tibble: 173 x 3
   name           species films                  
   <chr>          <chr>   <chr>                  
 1 Luke Skywalker Human   Revenge of the Sith    
 2 Luke Skywalker Human   Return of the Jedi     
 3 Luke Skywalker Human   The Empire Strikes Back
 4 Luke Skywalker Human   A New Hope             
 5 Luke Skywalker Human   The Force Awakens      
 6 C-3PO          Droid   Attack of the Clones   
 7 C-3PO          Droid   The Phantom Menace     
 8 C-3PO          Droid   Revenge of the Sith    
 9 C-3PO          Droid   Return of the Jedi     
10 C-3PO          Droid   The Empire Strikes Back
# ... with 163 more rows
```

Just like that we now have one film per row (with all the other values being repeated)! That is why our `tibble` went from `87` to `173` rows. And this `tibble` can be used to do calculations per film!

In this project, you will need this concept to work your way easily from rows that only have a "start year" and an "end year" to something usable for analysis.

`list-columns` is a topic that we can only brush on in this course. But if you want to learn more about them now, we cannot recommend enough these two videos (in this order):

-   [Garrett Grolemund - How to work with list columns](https://resources.rstudio.com/webinars/how-to-work-with-list-columns-garrett-grolemund)
-   [Jenny Bryan - Thinking inside the box](https://www.rstudio.com/resources/webinars/thinking-inside-the-box-you-can-do-that-inside-a-data-frame/)

## One more hint: `map`ping functions

Do you remember `map()`, the function from the `{purrr}` package that we used to apply functions to each element of a collection (like a `list` or a `vector`)? Let's do a quick recap.

Most functions in R can accept collections and happily do their job on each item. If I use the `ymd()` function from `{lubridate}`, we can give it one date or two thousands dates and it will work.

However, some functions are not designed to work with collections and this can be annoying. For example, if I have a list of websites that I want to download, I cannot give a `vector` with lots of urls to the `GET()` function of the `{httr}` package. It will crash and say:

> Hey, my `url` argument (which is `GET()`'s first argument) must be of length `1`!

...which basically its way to say that it can only accept one url at a time.

`map()` is a great workaround for such situations. `map()` takes a collection as its first argument and a function as its second argument. Then it applies the function to each element in the collection and wraps all the results in a new `list` that we can use for analysis.

So if we have three urls, we could do:

``` r
library(httr)
library(purrr)

urls <- c("www.google.com", "www.wikipedia.com", "www.extensionschool.ch")

map(urls, GET)
```

Note that we did not do:

``` r
map(GET(urls))
```

This is something that **always confuses people** when they discover `map()`. They will say:

> But I put it in `map()`! It should work now!

However if you do it this way, R will first execute `GET(urls)` and crash even before it ever gets the chance to run `map()`. That is why the collection and the function are **given separately**.

What does this mean if you want to use a function on two collections? For example, you want to use the function `seq()` which takes a start value (the argument is called `from=`) and an end value (the argument is called `to=`) and create a `vector` with all the values inbetween.

``` r
seq(1, 10)
```

```         
[1]  1  2  3  4  5  6  7  8  9 10
```

We did not spend time on `seq()` in the course, but we saw its cousin, the `:` operator (e.g. `1:10`).

`seq()`, like `GET()`, doesn't work on collections. If I give a `vector` of three values for `from=` and a `vector` of three values for `to=`, I will **not** get three `vector`s with all the values in between.

``` r
seq(c(1,10,100), c(3, 13, 103))
```

``` r
Error in seq.default(c(1, 10, 100), c(3, 13, 103)) :
  'from' must be of length 1
```

Can we use something like `seq()` with `{purrr}`? We sure can, with just one twist: `map()` only takes one collection. If you do:

``` r
map(c(1,10,100), c(3, 13, 103), seq)
```

`map()` will think that the second argument (i.e. `c(3, 13, 103)`) is the function you want to apply to the first argument... Needless to say that it will be terribly confused! But `map2()` is designed to take two arguments before getting a function, so this will work:

``` r
map2(c(1,10,100), c(3, 13, 103), seq)
```

```         
[[1]]
[1] 1 2 3

[[2]]
[1] 10 11 12 13

[[3]]
[1] 100 101 102 103
```

So **powerful**! Now I see you coming with:

> What if I have 3, 6 or 28 collections?

Realistically, this nearly never happens. Even `map2()` is rare. But if you want to venture into more collections, you can explore the `pmap()` function, which we won't cover here.

Why is `map2()` important if it is so niche? First we think understanding `map2()` helps you understanding `map()`. Second, you might want to use `mutate` and `seq()` with two columns from your `tibble` and get a result for each row in this project. Who knows, `map2()` could be handy if you ever found yourself in this situation...

------------------------------------------------------------------------

## Data collection

Connect to the database and store its contents in four separate tables. After that, inspect the contents of the columns and clean their contents carefully. This means that you have to keep the greatest possible amount of information.

**Hint**: Since this is a relational database, you will probably need to do a lot of `JOIN` operations between tables to obtain the information you need at the different stages. As this database is relatively small, we suggest that you `collect()` their contents immediately in four `tibbles` and then work with `{dplyr}`. That said, if you feel at ease with SQL operations, please feel free to use them to carry the operations before collecting.

## Exploring the assembly composition

### Part 1

On a first stage, let's look at the evolution of the active mandates in each of the assemblies. The `MANDATES` table has information about when people were elected, when their mandate ended and on which assembly they sat.

We will use a line plot to show how the number of people with an active mandate changed over the years. We won't bother working on exact dates, you can base your "active" years just on the `MANDATE_START_YEAR` and the `MANDATE_END_YEAR`, even if this creates some "bumps" in the line. This happens because some mandates are double counted in election years, as the old mandate ends and the new mandate starts.

You should obtain a plot similar to the example below. What can you see? Write down a few remarks.

![](https://d7whxh71cqykp.cloudfront.net/uploads/image/data/4199/04-project-politics-sketch.png)

**Hint 1**: You have the mandate start and end years in two columns of the `MANDATES` table. `map()`, `seq()` and `unnest()` can help you find the years in between, provided that the data is clean.

**Hint 2**: Look closely to the start and end years. Some information is missing! How to deal with this missing information? Write down your reasoning.

### Part 2

Let's explore the dynamics of the gender composition of these assemblies throughout the years. We will create a facet chart with one chart per assembly in each facet, one line for the active mandates held by women and another line for those held by men.

How has the gender composition of these assemblies evolved? Write down your remarks.

**Hint**: You will notice that having the same scale for all the axes inside each facet might not be ideal, as the assemblies have different sizes. You can overcome this problem using the argument `scales` in `facet_wrap()`.

### Part 3

Now it's time to analyse the Party composition of the assemblies. Let's focus first on the year 2000

1.  Create a stacked bar plot showing the percentage of active politicians per party in the year `2000`.Show it this by assembly.
    -   You'll notice that, there's quite a big number of parties, which makes reading the bar chart more challenging. This is why you want to group parties with a low percentage of affiliated active politicians (say \<2%) in a category "small parties".

    -   Moreover, since it is a bit hard to compare the proportions in the bars, it is useful to include the percentages in the plot, as a text or a label.
2.  Additionally, display these percentages in a table. There are several ways to create nice-looking tables in R notebooks. Have a look at the "Insert Tables" part in the [cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/rmarkdown.pdf) and choose your favorite.

What can you say about this plot? Write down your interpretations.

### Part 4

Let's take a look at the composition of the assemblies, not restricting to the year 2000, but over the years. This time, use a line plot to show how the percentages of party affiliations for active politicians have evolved over time.

Again, to avoid problems in the display with the big number of parties, collapse all small parties into a single category. This makes the plot easier to read and allows you to see better the dynamics.

Write down what you see from the 1980's onwards. How has the balance of power changed among the top 3 parties during this period?

## Analysing the Lifespans of Politicians

Let's analyse some aspects of the lives of our politicians. For the politicians that have a `YEAR_OF_DEATH` in the data, compute their life span.

### Part 5

Does the lifespan change if the politician has any `TITLE` ? Draw an appropriate plot to answer the question.

Then, run a statistical test to assess if the average life span is different for politicians who have a `TITLE` compared to those without any `TITLE`.

Before running the statistical test, write down what you expect the statistical results to look like on the basis of what you see in the plot. Does your expectation match the statistical test results?

**Hint**: To answer these questions, create a column that indicates whether the politician has a title or not. There's no need to analyse every possible title separately!

### Part 6

To refine your analysis, assign the politicians in two groups. First, those who were born before 1918, and a second group with the politicians who were born after 1918 .

Perform the analysis of the previous part. Do the same conclusions hold? Again, make sure to write down your expectation on the basis of the visualizations and confirm this with a test.

Is there a difference in results between the subgroup before 1918 and those born on or after 1918?

**Hint**: Use the variable `YEAR_OF_BIRTH` to create the groups. Also, you need to create a facet chart and and run two t-tests to answer: *is there a difference between politicians with and without title* in the group "born before 1918" and in the group "born after 1918"?

## Finding some facts about the Politicians

### Part 7

Over time, some politicians have been elected many times and therefore have held many mandates. Let's make a top 10 of the politicians who held multiple mandates and display it as a chart!

**Hint**: Rely on the `MANDATES` table.

### Part 8

That's a great chart! But it only tells part of the story. Some politicians might have held many mandates at the same time. In some cases this could be due to them having some overlap between mandates, or when the political system allows holding mandates across assemblies.

What politicians have held multiple mandates at the same time? Again, draw the top 10 in a chart.

**Hint**: With the `MANDATES` table you can construct the exact `START_DATE` and `END_DATE` of a politician's mandates and check if there is some overlap.

### Part 9

Finally, let's find the politicians who have changed ranks over the years. What politicians been affiliated to different parties over the years? Draw a chart with the top 10!

### Part 10

Take a sample of 20 politicians with a listed address and plot them on a map with `{leaflet}` or `{ggmap}`. You will need to use an API that converts the addresses to geocoordinates.

**Hint:** If you decide to use `{leaflet}`, please note that knitting to anything other than a `html_document` may lead to issues.

### A little reminder

Congratulations,you have now completed the second final project!

At this point, we strongly encourage you to refine your report, by adding some storytelling, focusing on the interpretations and curating your visualisations.
