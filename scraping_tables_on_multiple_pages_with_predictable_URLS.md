---
title: "scraping_tables_on_multiple_pages_with_predictable_URLS"
output: html_document
date: "2025-12-02"
---


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


## Scraping tables on multiple pages
### Scraping predictable URLs
But what if the table is spraed across multiple pages? There's a way to handle that, but it does required a bit more work than the previous example where all tables were on one page. To learning how to scrape webpages where the table is spread across multiple pages, we'll use this example: http://www.scrapethissite.com/pages/forms/?page_num=1. This link contains 24 pages with team statistics for all teams in the North American professional National Hockey League from year 1990 to year 2011.

To begin we must inspect the URL. Fortunately for us, each page has a base URL, and then it ends with a number that is the the page number. So e.g. the URL for the first page is http://www.scrapethissite.com/pages/forms/?page_num=1. The URL for the second page is http://www.scrapethissite.com/pages/forms/?page_num=2, and so on until page 24. We can isolate the base URL, which is http://www.scrapethissite.com/pages/forms/?page_num=, and then create 24 URLs, each with a number going from 1 to 24

``` r
paste0("http://scrapethissite.com/pages/forms/?page_num=", 1:24)
```

``` output
 [1] "http://scrapethissite.com/pages/forms/?page_num=1" 
 [2] "http://scrapethissite.com/pages/forms/?page_num=2" 
 [3] "http://scrapethissite.com/pages/forms/?page_num=3" 
 [4] "http://scrapethissite.com/pages/forms/?page_num=4" 
 [5] "http://scrapethissite.com/pages/forms/?page_num=5" 
 [6] "http://scrapethissite.com/pages/forms/?page_num=6" 
 [7] "http://scrapethissite.com/pages/forms/?page_num=7" 
 [8] "http://scrapethissite.com/pages/forms/?page_num=8" 
 [9] "http://scrapethissite.com/pages/forms/?page_num=9" 
[10] "http://scrapethissite.com/pages/forms/?page_num=10"
[11] "http://scrapethissite.com/pages/forms/?page_num=11"
[12] "http://scrapethissite.com/pages/forms/?page_num=12"
[13] "http://scrapethissite.com/pages/forms/?page_num=13"
[14] "http://scrapethissite.com/pages/forms/?page_num=14"
[15] "http://scrapethissite.com/pages/forms/?page_num=15"
[16] "http://scrapethissite.com/pages/forms/?page_num=16"
[17] "http://scrapethissite.com/pages/forms/?page_num=17"
[18] "http://scrapethissite.com/pages/forms/?page_num=18"
[19] "http://scrapethissite.com/pages/forms/?page_num=19"
[20] "http://scrapethissite.com/pages/forms/?page_num=20"
[21] "http://scrapethissite.com/pages/forms/?page_num=21"
[22] "http://scrapethissite.com/pages/forms/?page_num=22"
[23] "http://scrapethissite.com/pages/forms/?page_num=23"
[24] "http://scrapethissite.com/pages/forms/?page_num=24"
```
But what if the URLs were predictable, but did not increase one integer after another, starting from 1? we can handle this by creating a list of URLs using some functions in R, which will concatenate each element of URL, until we for each page have the full URL necessary

In this example we will create URLs which will allow us to download results from political elections in various states in The United States in 2012 and 2016. In this case, instead of number starting with 1 and then increasing one integer at a time, we need to specify in the URL which year the election took place and in which state it took place. To do this we first write the name of our new object. Now we need to concatenate a series of text strings. To indicate that we want to start concatenation we use the `crossing` function. The first part of our URL will be the base URL, which is https://www.example.com/. Then we add state to the URL. Then we draw a list of states, starting in alphabetical oorder with the first state, Alaska, and going onwards in alphabetical to the 5th state, which is California. Then we need to tell R that for each state, it should create 2 URLs. One with the year number 2012, and one with the year number 2016. So we put them as a vector. Then we need to tell R that it should unite all these elements into one URL, and that there should be no space between the elements that constitute the URL. When then call the URLs with the function `pull`

``` r
urls <-
  crossing("https://www.example.com/", 
           "?state=", 
           state.abb[1:5], 
           "?year", 
           c(2012, 2016)) %>% 
  unite("urls", sep = "") %>% 
  pull(urls)
```

Let us return to the data with hockey results. We know that there are 24 pages with tables containing the statistics, and we have a predictable URL, so we can easily create each URLs for each page.
But let us imagine that we have a predictable URL, but we don't know how many pages there. We need to write a script that will automatically find out how many pages there are. This is really useful if there are 100s of pages, and it would take too long to click to the last page in order to find out how many there are
Let us start be scraping the website


``` r
dat <- 
  bow("http://www.scrapethissite.com/pages/forms/") %>% 
  scrape()
```

We know need to go to the webpage in our internet browser and inspect the HTML
We find that there is an HTML element which shows how many pages there are in total. This element is called ".pagination>li". We can specify that the specific HTML element that we want to work with the function `html_elements`. Now we need to draw out the actual content of the selected HTML-element by using the function `html_text2`. The content that we draw out is a series of numbers, but they are in the format of character. We need to convert them to numbers by using `as.numeric` we now have a series of all page numbers, from 1 to 24. However, we are only interested in the last page number, which in our case has the highest numerical value. We can therefore isolate this number by using the function `max`


``` r
n_pages <- 
  dat %>% 
  html_elements(".pagination>li") %>% 
  html_text2() %>% 
  as.numeric() %>% 
  max(na.rm = TRUE)
```

``` warning
Warning in dat %>% html_elements(".pagination>li") %>% html_text2() %>% : NAs
introduced by coercion
```

Now we can create all the URLs by pasting our basic URL with the page numbers, going from 1 until the last page number. Our URLs become a vector with 24 elements, each element being the base URL with a number at the end

``` r
urls <- paste0("https://scrapethissite.com/pages/forms/?page_num=", 1:n_pages)
```

Now it is time to scrape data from all the URLs that we have created. First we write the name of our new object. Then we use the `map` function to tell R that it must do the scrape for each URL in our urls vector. The we use `bow` for each URL to ensure that scraping is allowed. Lastly, we scrape the pages with the `scrape` function

``` r
# downloading multiple pages
dat_all <- 
  map(urls, 
      ~ bow(.x) %>% 
        scrape())
```
The `map` function returns a list, where each element is a page.

Now we need to tell R that we want each element in the list to be combined with the other elements, so that our list can be turned into a dataframe. First we write the name of our new dataframe. Then we tell R that it should turn the list elements into a dataframe by using the `map_dfr` function. We tell R what the name of our list is, and that we want to draw the table from each element in the list, and then we draw the table with `html_table`

``` r
# formatting downloaded list into a dataframe
dat_tables <- 
  map_dfr(dat_all, 
          ~ html_elements(.x, "table") %>% 
            html_table())
```

