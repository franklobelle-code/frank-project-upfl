## Introduction

In this project, you will have to use an API to get your data from the web.

![](https://d7whxh71cqykp.cloudfront.net/uploads/image/data/2242/04-project-movie.png)

The API used in this project is the ["The Movie DB"](https://www.themoviedb.org). Unlike the APIs we used in the units, this one will ask you to create an account and use a `key` in all your queries (i.e., inside the URL). This is much closer to a real-life scenario, as most serious APIs use this kind of authentication system.

## Getting access to "The Movie DB"

First, you will need to create an account. You can use your `@extensionschool.ch` email for this.

![](https://d7whxh71cqykp.cloudfront.net/uploads/image/data/2243/04-project-movie-setup0.png)

You will probably be asked to verify your account (i.e., click on a link sent to your email and reenter your username and password).

Once this is done, you will have an account on "The Movie DB", but that is not enough to use the API. We now need to create an "API application" in the account (i.e., a project).

Creating an "application" is a common API pattern and many API providers (including Google and X) ask you to do such a thing. This is a way for them to see if you will use their data for commercial purposes (and charge you if so).

Go to the settings of your account by clicking on the little initial logo on the top right-hand side and click **"Settings"**.

![](https://d7whxh71cqykp.cloudfront.net/uploads/image/data/2244/04-project-movie-setup1.png)

In your **"Settings"** page, click on **"API"** in the left-hand side menu (➊), then **"Create"** (➋) and choose **"Developer"** (➌), since you are an individual and developing a not-for-profit project.

![](https://d7whxh71cqykp.cloudfront.net/uploads/image/data/2245/04-project-movie-setup2.png)

After you accept the "Terms and Conditions", you will be asked for some information. If you do not want to give out your personal details, please use the Extension School ones (as we checked with "The Movie DB" that this course was fair use):

![](https://d7whxh71cqykp.cloudfront.net/uploads/image/data/2247/04-project-movie-setup3.png)

-   **Type of Use:** Education (from the drop-down menu)
-   **Application name:** your EXTS username (or anything else)
-   **First Name:** First two letters of your username
-   **Last Name:** Last two letters of your username
-   **Application URL:** Since we do not plan to use the data in a web application, you can write `"localhost.com"` for the URL (it means that this application will only live on your computer).
-   **Application Summary**: Course project for EPFL EXTS
-   **Email Address:** This should be filled automatically with the email you provided for your account
-   **Phone Number:** 0216935041
-   **Address1:** EPFL Extension School
-   **Address2:** EPFL Innovation Park, Building E
-   **Zip Code:** 1015
-   **City:** Lausanne
-   **State:** Vaud
-   **Country:** Switzerland

If all went well, you should now have an API `Key` on your "Settings" page. Congratulations, you just created a *real* account on a *real* professional API!

![](https://d7whxh71cqykp.cloudfront.net/uploads/image/data/2246/04-project-movie-setup4.png)

The key you received for your project is a random string of letters and numbers (➊). You will have to use it in the URLs when you query the API: it is like your login/password to the service when you use the API.

So, to get the data from the movie with `ID` `550`, you cannot just send a `GET` request to this address:

```         
https://api.themoviedb.org/3/movie/550
```

Instead, you have to add an `api_key=` parameter at the end of all the URLs you use (using your personal key number---the one below is fake):

```         
https://api.themoviedb.org/3/movie/550?api_key=f193f4e58300
```

Like often with well-documented APIs, you can find a documentation listing the [parameters you can use for your queries](https://developer.themoviedb.org/reference/discover-movie) to help you understand what is possible. You will see the full list of parameters that you can use for filtering your results under `QUERY PARAMS`. As the page allows you to try requests, you find text fields to provide the input next to the parameters. On the right-hand side of the page, you can find different options for the request you'd like to send, such as `LANGUAGE` (i.e., the programming language you want to use for the request), `AUTHORIZATION`, `REQUEST`, and `RESPONSE`. The `Header` under `AUTHORIZATION`should be pre-filled with your API key or API Access Token once you are logged into your themoviedb account. Note that this input is required to be able to send requests. The request will change depending on the input you provide in the text boxes for the query parameters. As some of the text boxes are pre-filled, please feel free to clear the input fields you don't need for your query. Once you have provided input for all parameters you need for your query, you can click on "Try It" on the right-hand side to see the response. This is very useful to play around and get a feeling of how the queries can look in your solution for this report.

There are two types of parameters used in the query URLs:

-   parameters that take an "explicit" value, like `primary_release_year=` or `sort_by=`. When you read their values (e.g. `2014` or `popularity.desc`), you know straight away what is queried.
-   parameters that take an "ID" value, like `with_cast=`. When you read their values (e.g. `23659` or `878`), you don't really know what is queried if you don't know which ID means what.

This is common as well with APIs. Parameters that might have complicated/long/confusing spelling (like the title of a movie or the full name of an actor) often use an ID. What if two movies or two actors have the same name? That's also a situation where using IDs would help.

The problem is that you need to find these IDs before sending the query that you are interested in. And to do that you need to prepare another query. This is when you start reading [the full API documentation](https://developers.themoviedb.org/3/getting-started/introduction).

## Example

Let's say we want to write a query to get the highest-grossing movies with Tom Cruise from 2014. Using the page linked above, we first identify the required query parameters. When we use the `with_cast` param, it is better to use the ID of the actors instead of their name (which is not necessarily unique). To find the ID of an actor, we use [this page](https://developer.themoviedb.org/reference/search-person). When we enter the name of the actor we're interested in (in "query string.." which is a required field), we see the query on the right-hand side again. We send the request by clicking on "Try It!" to see the response and get the ID of the actor.

This is how the query could look like (replacing `YOUR_KEY` with your API key):

```         
https://api.themoviedb.org/3/discover/movie?api_key=YOUR_KEY&with_cast=500&primary_release_year=2014&sort_by=revenue.desc
```

Notice the `/discover` in the query URL: We're using the `discover` endpoint to discover all movies with the specified parameters. On other occasions, such as finding the ID of an actor, we might want to use the so-called `search` endpoint. The `search` endpoints let you search by name and find the IDs for different kinds of resources (companies, people, movie title...).

## Exercises

### Part 1

Inspired by the example query above, create queries to request the following information:

-   What are the highest-grossing dramas from 2010?
-   Have Will Ferrell and Liam Neeson ever been in a movie together?
-   Can you find kids' movies with Tom Cruise in it?

Answer the questions and include the queries and results (tibbles) in the report.

**Hints**:

-   When you query for movies of a specific genre, use the ID of the genre. To get the list of official movie genres and their IDs, visit [this page](https://developer.themoviedb.org/reference/genre-movie-list). Send the request on the right-hand side by using the "Try It!" button so the response with the IDs will be displayed below.

-   You need the following parameters to get the kids' movies: `certification_country=US` and `certification.lte=G`.

### Part 2

Write a query to find the ID of the animation movie company "Pixar". Show both the query and how you extract the ID from the result in your report. Add the ID as inline code in your report.

**Hint:** In this case, you should check the [`Search` endpoints](https://developers.themoviedb.org/3/search/search-companies).

### Part 3

Now that we have the ID of Pixar, we should be able to find all the movies they have worked on. Write a query to get all the Pixar movies and sort them by descending revenue. The result will be given to you as a JSON (parsed to a `list` by `{httr}`). Convert this list to a `tibble` so you have one row per film and one column per interesting piece of information. Show the query and the tibble in your report.

**Hints**:

-   To find out which parameters you need to use for your query, go again to the documentation for the [`/discover/movies` endpoint](https://developers.themoviedb.org/3/discover/movie-discover).

-   Have a close look at the keys in your `list`. You will notice that the API sends **"paginated" results** (i.e., look at these `page` and `total_pages` keys). It means that you never get more than `x` results at a time (at the time of this writing, this API sends `20` results at a time). Paginated APIs are **extremely** common as administrators don't want users to send queries that would require a ton of data and block the service for others for a long time. If you want to get the other pages, you need to play with the `page` parameter in your URL. Further, consider that if you want to repeat the same query over and over for different pages, `purrr::map()` is a useful function.

### Part 4

You may know that Pixar was acquired by Disney in 2006 after they had already been collaborating on films for more than a decade. For the last part of the report, we will look into whether this was a smart strategic decision by Disney by comparing the popularity of both Disney and Pixar films that came out from 2006 onwards.

-   First, acquire the "ID" for Disney using the `search` endpoint again. Note that if you try to find the company ID for Disney, there will be more than one result (Disney has many subsidiaries with the name "Disney" in it). For this exercise, specifically look for "Walt Disney Pictures" in the USA. Add the ID as inline code in your report.

-   Second, get the vote averages and vote counts for films from Walt Disney Productions and Pixar using the `discover/movies` endpoint. Use the API documentation again to find out how to get films from 2006 *onwards*.

-   Now, answer the following question: *Are the films from Pixar, on average, more popular than those from Walt Disney Pictures?* Compare the vote averages using boxplots. A suggestion would be to filter the data by including only films with a `vote_count` of at least 50. Consider that if only a few people voted on the film, the vote average would not be as representative as when many voted. After writing down your initial remarks based on the boxplot, state a statistical hypothesis and use a t-test to compare the average popularity for the two groups statistically. What is your conclusion? Do the test results confirm your observations? Was it a smart decision by Disney to acquire Pixar?
