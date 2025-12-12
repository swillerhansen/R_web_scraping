---
title: "Scraping tables on pages with unpredictable URLs"
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


## Scraping pages with unpredictable URLs

What if there are tables on multiple pages that we want to scrape, but the pagination does not give us a predictable URL? i.e. instead of a sequence of integers increasing by one, or letters change in alphabetical order or reverse alphabetic order, there is a custom sequence of letters and digits? There is a way to handle this so that we can scrape all pages in one action, but it requires some inspection of the data and writing some functions

We cannot anticipate what the all the various URLs will be, so we to find a method to automate the finding of all URLs, so that we can scrape all of them in one action.

Let us start by scraping the base URL

``` r
page1 <- 
  bow("http://www.scrapethissite.com/pages/forms/") %>% 
  scrape()
```

### Finding the link to the proceeding pages
In order to automate the finding of all URLs, we need to identify in the HTML where there is the button that allows us to go to the next page. We inspect the HTML of the webpage http://www.scrapethissite.com/pages/forms/ and see that there is an HTML element which describes the button that moves us to the next page. This button is a link. A link in HTML has is href. We see that HTML for the next button is aria-label='Next'. We need to extract the link from the button that goes to the next page. To do this we write the name of our new object. Then we write the name of object with the scraped webpage. Then we use `html_elements` o tell R that we want to work with the HTML that constitutes the Next-button. The last thing we need to do is to extract the link itself. To do this we use the function `html_attr` and write the HTML element for the link, which is `<href>`

``` r
# find the next button, write a CSS selector for it, and pull the value of href
next_page <- 
  page1 %>% 
  html_elements("[aria-label='Next']") %>% 
  html_attr("href")
```

### creating the set of URLs
Now that we have the pagination number for the first page, we need to concatenate it with the base URL, with the pagination number coming last

``` r
# verify that this is a full URL (starting with "http") that we can scrape, and it if not. 
# This is a root-relative path, so prefix it with the website's root URL
next_page <- 
  paste0("http://www.scrapethissite.com", 
         next_page)
```

### Finding the last page
We need to tell R that it should keep scraping pages until it comes to a page where there is no Next-button. So we scrape the last page and draw out the link


``` r
next_page_final <- 
  bow("http://www.scrapethissite.com/pages/forms/?page_num=24") %>% 
  scrape() %>% 
  html_elements("[aria-label='Next']") %>% 
  html_attr("href")
```

We need to test if the last page i.e. our objects are indeed our the last page. We use the function `is_empty`. If the object is the last page, it will give the result TRUE. If the object is not the last page it will give us the value FALSE

``` r
# testing if condition for both are correct. next_page_final should be TRUE, next_page should be FALSE
is_empty(next_page_final)
```

``` output
[1] TRUE
```

``` r
is_empty(next_page)
```

``` output
[1] FALSE
```

### Assembling the URLs
Now we need to tell R that it should scrape a page, find the URL for the next page and then scrape that next page until it finds the last page. 

First we create an empty list in which our scraped data will be placed into. Then we tell R that the first page to download is the URL for the first page. Then we write our own function. We tell R that it should run this function if the page it scrapes has a page coming after that. Then it should create a link by pasting the base URL with the number of the next pages. Then it should the link that it has created by the `paste0` function. The downloaded page should be stored in a list. Then from the scraped webpage it should draw out the link for the next-page button. 

``` r
all_dat <- list()
next_page <- "/pages/forms/?page_num=1"
i <- 0
# proceed if there is a next page from the last scrape
while (!is_empty(next_page)) {
  # increase iteration count
  i <- i + 1
  
  # assemble URL
  link <- 
    paste0("http://www.scrapethissite.com", 
           next_page)
  
  # download current page
  dat <- 
    bow(link) %>% 
    scrape()
  
  # store downloaded page in list
  all_dat[[i]] <- dat
  
  # get link for next page
  next_page <- 
    dat %>% 
    html_elements("[aria-label='Next']") %>% 
    html_attr("href")
  
  # delay for five seconds before next download
  Sys.sleep(5)
}
```

Now we need to paste the 24 pages into a vector, each element in the vector being a page

``` r
# downloading multiple pages
urls <- paste0("http://scrapethissite.com/pages/forms/?page_num=", 1:24)
```

### scraping the URLs
Now we scrape each of the 24 URLs that we have created. We have 24 elements in our URL vector, so we use the `map` function to tell R that it should scrape each URL in our URL vector. 

``` r
dat_all <-
  map(urls,
      ~ bow(.x) %>%
        scrape())
```

Now we need to tell R that for each page in our list element in our scraped data, it should draw out the table, and combine them together into one dataframe with the `map_dfr` function

``` r
# go through each element of the page list and pull out a dataframe
# and then row-bind all of them together
dat_tables <- 
  map_dfr(all_dat, 
          ~ html_elements(.x, "table") %>% 
            html_table())
```

