---
title: "Scraping tables on a page"
output: html_document
date: "2025-12-02"
---


## Scraping tables on a single page
## Introduction
Lets us start by installing the libraries that will give us the necessary functionality to do web scraping

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
library(polite)
library(rvest)
library(tidyverse)
library(purrr)
library(htmlTable)
library(htmltools)
library(scales)
```

### Scraping multiple tables on one page
One of the formats that data on a website often come in is a table

Let's look at statics about students at The University of Copenhagen (UCPH) at this page:
https://om.ku.dk/tal-og-fakta/studerende/
The html element for a table is simply called `<table>`
We can use this HTML tag to scrape a table using `html_table`

We see on the website that there are multiple elements that can be clicked to reveal some of the stats for UCPH students. 

We want to see what HTML tags that the stats from the website have. 
We can use our internet browser's ability to inspect the HTML of any website that we visit. This is the HTML that generates the website that we see when we type a URL and go to the website. Most internet browsers are able to inspect a website's HTML. If the browser that you use cannot do this, then try to do it with a different browser

When we inspect the HTML, we see that there are many tables in HTML, each corresponding to one drop-down part on the webpage

Fortunately for us, the function `html_table` will scrape all the tables on the page

The first thing we need to do is to scrape the webpage. One of the challenges of webscraping is that sometimes, certain webpages won't allow scraping. This prohibition can be for the entire webpage with all its subpages, or it can be for certain subpages. It can be quite laborious read and check this yourself, but fortunately for us, the polite package allows os to automatize this. By using its function `bow` before we scrape, it will check if scraping is allowed. If scraping is allowed, the scraping will go ahead. If scraping is not allowed, no scraping will take place.
So let us start by writing the name of our object. Then we use bow and write the link of the page that we want to scrape, in order to check if scraping is allowed. Then we scrape the webpage

``` r
dat <-
  bow("https://om.ku.dk/tal-og-fakta/studerende/") %>%
  scrape()
```

### Handling the Danish decimal separators format
But before we go further with our data, we need to make sure that R will handle decimal separators correctly. The default standard in R for handling decimal separators is to use the American version, where dot is the decimal separator, and comma groups larger numbers together, making it easier for the human eye to read numbers that are from one thousand and above. In the tables that we want to scrape here, the Danish format is used, where comma is the decimal separator, and dot groups larger numbers together. We there need to start by telling R that it should use the Danish format

``` r
dansk_locale <- locale(decimal_mark = ",", 
  grouping_mark = ".", 
  date_names = "da", 
  date_format = "%d-%m-%Y", 
  time_format = "%H:%M:%S", 
  tz = "Europe/Copenhagen")
```

The `scrape` function scrapes everything on the webpage. So now we need to tell R which part of the HTML that we want to work with. To specify that we want to work with all the tables from the webpage, we can use the function `html_table`. This function scrapes all the tables on the page. So let us first write the name of our new object. Then we tell R to work the the scraped data, which we called dat. Lastly, we tell R that it should take the tables form the scraped webpage.

``` r
tabeller <- 
  dat %>%  
  html_table(fill = TRUE, convert = FALSE)
```

Now we have an object with the tables, which is a list. Each table is a separate element in the list. However, we want all the tables to be merged into one dataframe. To do this we use `bind_rows`


``` r
tabeller <- tabeller %>% bind_rows()
```
Now we have a dataframe with 2 columns. The first column, called X1 contains the the statistic that was calculated. The second column, called X2, contains the number of the calculated statistic. 

We see that some statistics are percentages, and other are absolute numbers. We need to handle this in order to do analysis. To do this we need to do a couple of steps. First, we create a new column that tells if the measured statistic is an absolute number of a percentage. we call the new column X3. Then we need to remove the percentage sign from column X2. Lastly, to make sure that the X2 column can be used for calculations, we trim any unintended whitespace with `str_trim`.

``` r
tabeller <- tabeller %>% 
  mutate(X3 = str_detect(X2, "%")) %>% 
  mutate(X2 = str_remove(X2, "%"), 
         X2 = str_trim(X2))
```

Now that we have scraped and cleaned our data, we need to tell R that it should use the Danish format for numbers, date format, and use the time zone for Copenhagen

``` r
tabeller <- type_convert(tabeller, locale = dansk_locale)
```

``` output

── Column specification ────────────────────────────────────────────────────────
cols(
  X1 = col_character(),
  X2 = col_number()
)
```

