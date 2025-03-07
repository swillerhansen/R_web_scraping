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
Successfully installed 1 package in 7 milliseconds.
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
Successfully installed 1 package in 6.1 milliseconds.
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
Successfully installed 1 package in 6.1 milliseconds.
```

``` r
install.packages("purrr")
```

``` output
The following package(s) will be installed:
- purrr [1.0.4]
These packages will be installed into "~/work/R_web_scraping/R_web_scraping/renv/profiles/lesson-requirements/renv/library/linux-ubuntu-jammy/R-4.4/x86_64-pc-linux-gnu".

# Installing packages --------------------------------------------------------
- Installing purrr ...                          OK [linked from cache]
Successfully installed 1 package in 6.2 milliseconds.
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
Successfully installed 1 package in 7 milliseconds.
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
Successfully installed 1 package in 6.1 milliseconds.
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
Successfully installed 1 package in 7.3 milliseconds.
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



Let us start by writing the main URL and see if webscraping is allowed

``` r
bow("https://en.wikipedia.org/wiki/Proposed_United_States_acquisition_of_Greenland")
```

``` output
<polite session> https://en.wikipedia.org/wiki/Proposed_United_States_acquisition_of_Greenland
    User-agent: polite R package
    robots.txt: 464 rules are defined for 33 bots
   Crawl delay: 5 sec
  The path is scrapable for this user-agent
```

We see that scraping is allowed, so let us scrape the main page

``` r
dat <- bow("https://en.wikipedia.org/wiki/Proposed_United_States_acquisition_of_Greenland") %>% 
  scrape()
```

write new text here

``` r
greenland_us_wiki <- html_elements(dat, "h1, h2, h3, h4, p")
```

write new text here

``` r
df_greenland_us <- tibble(
  tag = html_name(greenland_us_wiki),  # Extracts tag names (h1, h2, h3, h4, p)
  text = html_text(greenland_us_wiki, trim = TRUE)  # Extracts clean text
)
```

write new text here

``` r
df_greenland_us <- df_greenland_us %>%
  mutate(
    h1 = ifelse(tag == "h1", text, NA),
    h2 = ifelse(tag == "h2", text, NA),
    h3 = ifelse(tag == "h3", text, NA),
    h4 = ifelse(tag == "h4", text, NA)
  ) %>%
  fill(h1, .direction = "down") %>%  # Fill down h1 for all content
  fill(h2, .direction = "down") %>%  # Fill down h2 until new h2 appears
  fill(h3, .direction = "down") %>%  # Fill down h3 until new h3 appears
  fill(h4, .direction = "down")      # Fill down h4 until new h4 appears
```

write new text here

``` r
# Step 1: Remove everything before the first h1
df_greenland_us <- df_greenland_us %>%
  filter(cumsum(tag == "h1") > 0)  # Keep rows after the first h1 appears
```

write new text here

``` r
# Step 2: Find where "See also" appears in an h2, h3, or h4 and remove everything after
see_also_row <- which(df_greenland_us$tag %in% c("h2", "h3", "h4") & 
                        str_detect(str_to_lower(df_greenland_us$text), "^see also"))
```

write new text here

``` r
# Only proceed if "See also" is found
if (length(see_also_row) > 0) {
  df_greenland_us <- df_greenland_us %>% slice(1:(min(see_also_row) - 1))  # Keep only rows before "See also"
}
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
