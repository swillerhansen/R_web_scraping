---
title: 'Tables'
teaching: 10
exercises: 2
---





:::::::::::::::::::::::::::::::::::::: questions 

- How do you write a lesson using R Markdown and `{sandpaper}`?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Explain how to use markdown with the new lesson template
- Demonstrate how to include pieces of code, figures, and nested challenge blocks

::::::::::::::::::::::::::::::::::::::::::::::::

## Introduction
Lets us start by installing the libraries that will give us the necessary functionality to do webscraping

``` r
install.packages("polite")
install.packages("rvest")
install.packages("tidyverse")
install.packages("purrr")
install.packages("htmlTable")
install.packages("htmltools")
install.packages("scales")
```


Lets us now activate our installed libraries to activate their functionality

``` r
# library(polite)
# library(rvest)
# library(tidyverse)
# library(purrr)
# library(htmlTable)
# library(htmltools)
# library(scales)
```

One of the formats that data on a website often come in is a table

Let's look at statics about students at The University of Copenhagen (UCPH) at this page:
https://om.ku.dk/tal-og-fakta/studerende/
The html element for a table is simply called <table>
We can use this HTML tag to scrape a table using \html_table\

We see on the website that there are multiple elements that can be clicked to reveal some of the stats for UCPH students. 

We want to see what HTML tags that the stats from the website have. 
We can use our internet browser's ability to inspect the HTML of any website that we visit. This is the HTML that generates the website that we see when we type a URL and go to the website. Most internet browsers are able to inspect a website's HTML. If the browser that you use cannot do this, then try to do it with a different browser

When we inspect the HTML, we see that there are many tables in HTML, each corresponding to one drop-down part on the webpage

Fortunately for us, the function \html_table\ will scrape all the tables on the page

The first thing we need to do is to scrape the webpage. One of the challenges of webscraping is that sometimes, certain webpages won't allow scraping. This prohibition can be for the entire webpage with all its subpages, or it can be for certain subpages. It can be quite laborious read and check this yourself, but fortunately for us, the polite package allows os to automatize this. By using its function \bow\ before we scrape, it will check if scraping is allowed. If scraping is allowed, the scraping will go ahead. If scraping is not allowed, no scraping will take place.
So let us start by writing the name of our object. Then we use bow and write the link of the page that we want to scrape, in order to check if scraping is allowed. Then we scrape the webpage

``` r
dat <-
  bow("https://om.ku.dk/tal-og-fakta/studerende/") %>%
  scrape()
```

``` error
Error in bow("https://om.ku.dk/tal-og-fakta/studerende/") %>% scrape(): could not find function "%>%"
```

But before we go further with our data, we need to make sure that R will handle decimal separators correctly. The default standard in R for handling decimal separators is to use the American version, where dot is the decimal separator, and comma groups larger numbers together, making it easier for the human eye to read numbers that are from one thousand and above. In the tables that we want to scrape here, the Danish format is used, where comma is the decimal separator, and dot groups larger numbers together. We there need to start by telling R that it should use the Danish format

``` r
dansk_locale <- locale(decimal_mark = ",", grouping_mark = ".", date_names = "da", date_format = "%d-%m-%Y", time_format = "%H:%M:%S", tz = "Europe/Copenhagen")
```

``` error
Error in locale(decimal_mark = ",", grouping_mark = ".", date_names = "da", : could not find function "locale"
```




This is a lesson created via The Carpentries Workbench. It is written in
[Pandoc-flavored Markdown][pandoc] for static files (with extension `.md`) and
[R Markdown][r-markdown] for dynamic files that can render code into output
(with extension `.Rmd`). Please refer to the [Introduction to The Carpentries
Workbench][carpentries-workbench] for full documentation.

What you need to know is that there are three sections required for a valid
Carpentries lesson template:

 1. `questions` are displayed at the beginning of the episode to prime the
    learner for the content.
 2. `objectives` are the learning objectives for an episode displayed with
    the questions.
 3. `keypoints` are displayed at the end of the episode to reinforce the
    objectives.

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Inline instructor notes can help inform instructors of timing challenges
associated with the lessons. They appear in the "Instructor View"

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge 


``` r
# here you can write some code
```


## Challenge 1: Can you do it?

What is the output of this command?

```r
paste("This", "new", "lesson", "looks", "good")
```

:::::::::::::::::::::::: solution 

## Output
 
```output
[1] "This new lesson looks good"
```

:::::::::::::::::::::::::::::::::


## Challenge 2: how do you nest solutions within challenge blocks?

:::::::::::::::::::::::: solution 

You can add a line with at least three colons and a `solution` tag.

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

## Figures

You can include figures generated from R Markdown:


``` r
pie(
  c(Sky = 78, "Sunny side of pyramid" = 17, "Shady side of pyramid" = 5), 
  init.angle = 315, 
  col = c("deepskyblue", "yellow", "yellow3"), 
  border = FALSE
)
```

<div class="figure" style="text-align: center">
<img src="fig/tables-rendered-pyramid-1.png" alt="pie chart illusion of a pyramid"  />
<p class="caption">Sun arise each and every morning</p>
</div>
Or you can use pandoc markdown for static figures with the following syntax:

`![optional caption that appears below the figure](figure url){alt='alt text for
accessibility purposes'}`

![You belong in The Carpentries!](https://raw.githubusercontent.com/carpentries/logo/master/Badge_Carpentries.svg){alt='Blue Carpentries hex person logo with no text.'}

## Math

One of our episodes contains $\LaTeX$ equations when describing how to create
dynamic reports with {knitr}, so we now use mathjax to describe this:

`$\alpha = \dfrac{1}{(1 - \beta)^2}$` becomes: $\alpha = \dfrac{1}{(1 - \beta)^2}$

Cool, right?

::::::::::::::::::::::::::::::::::::: keypoints 

- Use `.md` files for episodes when you want static content
- Use `.Rmd` files for episodes when you need to generate output
- Run `sandpaper::check_lesson()` to identify any issues with your lesson
- Run `sandpaper::build_lesson()` to preview your lesson locally

::::::::::::::::::::::::::::::::::::::::::::::::

