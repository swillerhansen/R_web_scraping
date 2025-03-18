---
title: "Paragraph"
output: html_document
date: "2024-12-13"
---

## Introduction

``` r
install.packages("polite")
install.packages("rvest")
install.packages("tidyverse")
install.packages("purrr")
install.packages("htmlTable")
install.packages("htmltools")
install.packages("scales")
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


We have found a Wikipedia article in English about plans from United States presidents to acquire Greenland. Wikipedia articles are usually licensed with a Creative Commons license. We therefore expect that we are allowed to scrape the page. 

Let us start by using `bow` to check if the page allows scraping

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

### scraping the URL
We see that scraping is allowed, so let us scrape the main page

``` r
dat <- bow("https://en.wikipedia.org/wiki/Proposed_United_States_acquisition_of_Greenland") %>% 
  scrape()
```

### specifying HTML elements to be extracted from the scraped webpage
We have successfully scraped the page. Now we need to extract the HTML elements that we are interested in. In HTML the headers elements start with <h>. The headers exist in a hierarchical fashion. The most overall header is called <h1>. The second most overall header is called <h2> and so on. On the Wikipedia page the headers are <h1>, <h2>, <h3>, and <h4>. So we need to specify each of them when we extract the HTML elements from the our scrape. The headers are usful for designating which sections the article's text is divided into. But they do not contain the article's text. The article's text is in the HTML element <p>. But all the text is not found in one <p> element. It is divided into multiple paragraphs. This is because the text is then displayed in separate paragraphs on the page, which makes the article more readable to the human eye. 

We can write p to extract all paragraphs

``` r
greenland_us_wiki <- html_elements(dat, "h1, h2, h3, h4, p")
```

We now have the headers and the paragraphs extracted. Now we need to convert their content into a readable format so that we can work with the text. To do this we use the function `html_text`. This extracts the content of the HTML elements that specified before, i.e. headers and paragraphs. But only having the text content will make us able to discern which texts are headers and which are paragraphs. We will therefore need to use `html_name` to give us each text's HTML element. 

The best way to format this data is to make it a tibble, which is type of data frame. We therefore create a tibble where the HTML tag is in one column, and the text of that HTML element is in another columns. 

``` r
df_greenland_us <- tibble(
  tag = html_name(greenland_us_wiki),  # Extracts tag names (h1, h2, h3, h4, p)
  text = html_text(greenland_us_wiki, trim = TRUE)  # Extracts clean text
)
```

Now we have the right alignment of rows and columns where each row is an HTML element with its corresponding text content. 

But we would like to have a better understanding and overview of which paragraphs are found under which headings. To do this let us first create a new set of columns. One column for each header, using `mutate`

Now we need to make sure that each of these header columns has all its cells filled out. This will allow us to for each paragraph see under which header 1 it is and under which header 2 it is under and so on. To do this we use the `fill` function. Fill allows us to for each of the header columns extend the text value in a cell through all the succeeding empty cells until it reaches a cell that already has text in it. To specify this direction we write that the `.direction` should be downwards. 

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

We see that for some unknown reason the first header in the data frame is <h2> and not <h1>. By looking at the article page we see that <h1> is the proper header of the article that encompasses all its content. So we need to remove the rows that come before <h1>

``` r
# Step 1: Remove everything before the first h1
df_greenland_us <- df_greenland_us %>%
  filter(cumsum(tag == "h1") > 0)  # Keep rows after the first h1 appears
```

### filtering away unwanted columns
In the bottom of our dataframe, we see that there are some rows that are paragraphs and headers but they do not form part of the article text itself. We need to remove them. Because the rows come at then end of the dataframe, we can simply find the first of these superfluous rows and delete it and everything after that.

When this is the case, there is in a header, instead of a usual header text there is the text "See also". To find the row where "See also" is the text, we use the `which` function to search the text cells in rows where the tag is a header (except <h1>) and the text starts with "see also". We specify with "^" that the text must begin with "see also". We convert the text to lowercase to increase the probability of a match. R is very literal, so it treats "See also" and "see also" as 2 entirely different strings.

``` r
# Step 2: Find where "See also" appears in an h2, h3, or h4 and remove everything after
see_also_row <- which(df_greenland_us$tag %in% c("h2", "h3", "h4") & 
                        str_detect(str_to_lower(df_greenland_us$text), "^see also"))
```

We now see the number of rows there are until we reach "see also". We can now use this knowledge to do a `slice` so that we only retain the article's proper text. We start by specifying with `if` that this action should only be conducted if there is actually a "see also" found in the scrape. If there is not, then nothing should be done. 

We use `slice` to tell R that it should keep the rows beginning from row 1 all the way to the lowest number in see_also_row. The lowest number in see_also_row is the row that contains "see also". If we were to do this, we would retain the "see also" row in our dataframe but remove everything after it. But the row with "see also" should also be removed. So we use -1 to tell R that it should not include the last row in the slice, i.e. the row containing "see also".


``` r
# Only proceed if "See also" is found
if (length(see_also_row) > 0) {
  df_greenland_us <- df_greenland_us %>% slice(1:(min(see_also_row) - 1))  # Keep only rows before "See also"
}
```

Now we have our data frame in the final format. We can analyze the headers to see which topics are described in the text. We can use text mining methods to analyze how the various topics are described, whether the words used in the various sections are positive or negative, or other sentiments expressed in the text. We can also count the number of words under each <h2> or <h3>, to see which topics are discussed the most. 
