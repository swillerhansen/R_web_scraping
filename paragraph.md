---
title: "Paragraph"
output: html_document
date: "2024-12-13"
---

## Introduction

``` r
install.packages("polite")
```

``` output
The following package(s) will be installed:
- polite [0.1.3]
These packages will be installed into "~/work/R_web_scraping/R_web_scraping/renv/profiles/lesson-requirements/renv/library/linux-ubuntu-jammy/R-4.4/x86_64-pc-linux-gnu".

# Installing packages --------------------------------------------------------
- Installing polite ...                         OK [linked from cache]
Successfully installed 1 package in 7.7 milliseconds.
```

``` r
install.packages("rvest")
```

``` output
The following package(s) will be installed:
- rvest [1.0.4]
These packages will be installed into "~/work/R_web_scraping/R_web_scraping/renv/profiles/lesson-requirements/renv/library/linux-ubuntu-jammy/R-4.4/x86_64-pc-linux-gnu".

# Installing packages --------------------------------------------------------
- Installing rvest ...                          OK [linked from cache]
Successfully installed 1 package in 6.9 milliseconds.
```

``` r
install.packages("tidyverse")
```

``` output
The following package(s) will be installed:
- tidyverse [2.0.0]
These packages will be installed into "~/work/R_web_scraping/R_web_scraping/renv/profiles/lesson-requirements/renv/library/linux-ubuntu-jammy/R-4.4/x86_64-pc-linux-gnu".

# Installing packages --------------------------------------------------------
- Installing tidyverse ...                      OK [linked from cache]
Successfully installed 1 package in 6.7 milliseconds.
```

``` r
install.packages("purrr")
```

``` output
The following package(s) will be installed:
- purrr [1.0.2]
These packages will be installed into "~/work/R_web_scraping/R_web_scraping/renv/profiles/lesson-requirements/renv/library/linux-ubuntu-jammy/R-4.4/x86_64-pc-linux-gnu".

# Installing packages --------------------------------------------------------
- Installing purrr ...                          OK [linked from cache]
Successfully installed 1 package in 6.6 milliseconds.
```

``` r
install.packages("htmlTable")
```

``` output
The following package(s) will be installed:
- htmlTable [2.4.3]
These packages will be installed into "~/work/R_web_scraping/R_web_scraping/renv/profiles/lesson-requirements/renv/library/linux-ubuntu-jammy/R-4.4/x86_64-pc-linux-gnu".

# Installing packages --------------------------------------------------------
- Installing htmlTable ...                      OK [linked from cache]
Successfully installed 1 package in 6.8 milliseconds.
```

``` r
install.packages("htmltools")
```

``` output
The following package(s) will be installed:
- htmltools [0.5.8.1]
These packages will be installed into "~/work/R_web_scraping/R_web_scraping/renv/profiles/lesson-requirements/renv/library/linux-ubuntu-jammy/R-4.4/x86_64-pc-linux-gnu".

# Installing packages --------------------------------------------------------
- Installing htmltools ...                      OK [linked from cache]
Successfully installed 1 package in 6.7 milliseconds.
```

``` r
install.packages("scales")
```

``` output
The following package(s) will be installed:
- scales [1.3.0]
These packages will be installed into "~/work/R_web_scraping/R_web_scraping/renv/profiles/lesson-requirements/renv/library/linux-ubuntu-jammy/R-4.4/x86_64-pc-linux-gnu".

# Installing packages --------------------------------------------------------
- Installing scales ...                         OK [linked from cache]
Successfully installed 1 package in 6.7 milliseconds.
```


``` r
library(polite)
library(rvest)
library(tidyverse)
library(purrr)
library(htmlTable)
library(htmltools)
library(scales)
```

In this part of the course, we will now look at how to scrape a few  different HTML elements. Specifically, we will look at how to scrape paragraphs and headers. A paragraph is an HTML element that often contains a bulk of text that we can be interested in when we scrape a webpage. We will also scrape headers, which is an HTML element that often describes the content of the webpage or the content of other HTML elements on webpage.

In this part, we will learn how to scrape debates from US presidential elections. On the webpage https://www.presidency.ucsb.edu/documents/presidential-documents-archive-guidebook/presidential-campaigns-debates-and-endorsements-0, there are transcripts of all utterances in debates between candidates for the US presidential election, all the way from the first debate between Kennedy and Nixon in 1960 to the debates between Harris and Trump in 2024. In this case, we are fortunate that all debate have the same base URL, and added to it details of that particular debate. The base URL is https://www.presidency.ucsb.edu, and after it in the URL comes details about the particular debate, such as location. 

Let us start by writing the main URL and see if webscraping is allowed

``` r
main_url <- "https://www.presidency.ucsb.edu/documents/presidential-documents-archive-guidebook/presidential-campaigns-debates-and-endorsements-0"

bow(main_url)
```

``` output
<polite session> https://www.presidency.ucsb.edu/documents/presidential-documents-archive-guidebook/presidential-campaigns-debates-and-endorsements-0
    User-agent: polite R package
    robots.txt: 69 rules are defined for 1 bots
   Crawl delay: 10 sec
  The path is scrapable for this user-agent
```

We see that scraping is allowed, so let us scrape the main page

``` r
main_page <- bow(main_url) %>%
scrape()
```

Now we need to get the URL for each individual debate. The HTML element for a link is href. By inspecting the HTML in our browser, we see that the links for the debates are found within the HTML elements <td and <a>. So first we need to select the HTML elements with \html_elements\ and write the HTML element. Then we need to extract the link with \html_attr\ and wite the HTML for link, which is href

``` r
links <- main_page %>%
  html_elements("td a") %>%  # Find <a> tags inside <td>
  html_attr("href")       # Get the 'href' attribute (the link URL)
```

We see that there are more debates on the page than just presidential debates. There are also vice presidential debates, and there also debates within the Democratic Party and Republican Party for candidates who want to have the party's mandate to be its presidential candidate. We have all the URLs for the various debates in our vector called links. Now we need to create a new vector where we have filtered to only include URLs for the presidential debates. First we write the base URL. Then we create create the URLs for the presidential debate by joining the base URL with more part of the URL for presidential debates, We are fortunate in that all URLs for presidential debates contain the same secondary part of a URL. Therefore we filter to all URLs that have this part about presidential election in them

``` r
base_url <- "https://www.presidency.ucsb.edu"
filtered_links <- links[grepl("/documents/presidential-debate-", links)]  # Only keep debate-related URLs
```

We need to make sure that all links start with https://. So we use the \ifelse\ function to specify that if a link in our vector of filtered links starts with https:// it should be kept. If a link does not start with https://, it should have the first part of the URL concatenated with the link

``` r
# Ensure proper formatting with "https://"
full_links <- ifelse(grepl("^https", filtered_links), filtered_links, paste0("https://www.presidency.ucsb.edu", filtered_links))
```



## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:


``` r
summary(cars)
```

``` output
     speed           dist       
 Min.   : 4.0   Min.   :  2.00  
 1st Qu.:12.0   1st Qu.: 26.00  
 Median :15.0   Median : 36.00  
 Mean   :15.4   Mean   : 42.98  
 3rd Qu.:19.0   3rd Qu.: 56.00  
 Max.   :25.0   Max.   :120.00  
```

## Including Plots

You can also embed plots, for example:

<img src="fig/paragraph-rendered-pressure-1.png" style="display: block; margin: auto;" />

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
