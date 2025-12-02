---
title: 'Tables'
teaching: 10
exercises: 2
---





:::::::::::::::::::::::::::::::::::::: questions 

- What is HTML?
- How do you scrape tables from a web page

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Understand the basics of HTML and how to inspect a web page's HTML
- Scrape tables on a webpage, including when the tables aer spread across multiple pages

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
library(polite)
library(rvest)
library(tidyverse)
library(purrr)
library(htmlTable)
library(htmltools)
library(scales)
```

### Scraping multiple tables on one page
test
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
Now we need to tell R that it should scrape a page, finds the URL for the next page and then scrape that next page until it finds the last page. 

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

