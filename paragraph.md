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
Successfully installed 1 package in 6.4 milliseconds.
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
Successfully installed 1 package in 5.4 milliseconds.
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
Successfully installed 1 package in 5.8 milliseconds.
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
Successfully installed 1 package in 5.5 milliseconds.
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
Successfully installed 1 package in 6.3 milliseconds.
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
Successfully installed 1 package in 5.3 milliseconds.
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
Successfully installed 1 package in 5.5 milliseconds.
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

Our scrape ´d result will be stored as a list before we convert it to a dataframe. So we need to create an empty list that we'll later put our data in

``` r
# Loop through each filtered link and scrape the content from each subpage
results <- list()  # Create a list to store the scraped results
```

Now we need to make our own functions that will help us scrape all webpages with presidential debates at once instead of scraping each individual debate one after another. To do this we need to create 2 functions. The first one will defines how to scrape a particular webpage. First we give the function an appropriate name. Then we tell R that it's a function and that to use this function a link must be written. Then we tell R that it should use \bow\ to check that the linked webpage allows scraping. Then we take that result and scrape the page with \scrape\ and save it as an object. Now we need to tell R which parts of the HTML it should draw out of the scraped data. We want to draw a header which is <h1>; This will help us keep track of which individual debates that the presidential candidates' utterances were said. Next we need to scrape the date of the debate, which is found in the HTML element <span>. Lastly, we need to scrape the utterances from the debate, which is found in the HTML element <p>, which means a paragraph. Then we trim the result to make it more readable. Lastly, we save the result by using the \return\ function

``` r
# Define a function to scrape a single subpage
scrape_debate_page <- function(link) {
  
  # Be polite and create a new session for the subpage
  subpage_session <- bow(link)
  
  # Scrape the subpage
  subpage <- scrape(subpage_session)
  
  # Extract specific content from <h1>, <span>, and <p> tags
  debate_text <- subpage %>%
    html_elements("h1, span, p") %>%   # Use a single combined CSS selector
    html_text(trim = TRUE)
  
  return(debate_text)
}
```

Now we can use our function of scraping a debate page to create a new function that scrapes all the debate pages. First we tell R that it should give an appropriate name to this function. Then we stipulate that the full URL of the pages to be scraped must be written, by calling an object with the all the full URLs that we created previously. The first thing that the function does is to create an emtpy list to store the scraped data. Then, by using the \for\ we stipulate that this function should be applied to each individual link in our object of full_links object. We use the previous function we told R how to scrape each debate and tell R that it should use that function to scrape each of the full_links. The result should then be stored in the list that we created earlier in the function. Lastly, we use \Sys.sleep\ to avoid the webpages from scraping. 

``` r
# Main scraping process
scrape_all_debates <- function(full_links) {
  results <- list()  # Create an empty list to store the results
  
  for (link in full_links) {
    
    # Call the scrape_debate_page function for each link
    debate_text <- scrape_debate_page(link)
    
    # Store the debate text in the results list with the link as the name
    results[[link]] <- debate_text #possibly format and clean the data before the result is saved
    
    # Optional: Add a delay between requests to avoid overwhelming the server
    Sys.sleep(1)
  }
  
  return(results)
}
```

Now that we have our function that will allow us to scrape all the webpages in full_links, we use that function scrape the pages and save the result

``` r
presidential_debates <- scrape_all_debates(full_links)
```



``` r
# I want to delete all rows that contains the string "https://" in the column Content
# and I also want to delete the rows that come after the rows that contain the string "https://"
clean_speech <- function(presidential_df){
  presidential_df %>% 
    mutate(Speaker = str_extract(value, "[A-Z]+:")) %>% 
    mutate(cleaned = str_detect(value, "^This transcript|https://")) %>% 
    mutate(cleaned = if_else(cleaned == FALSE, NA, cleaned)) %>% 
    fill(cleaned, .direction = "down") %>% 
    filter(is.na(cleaned)) %>% 
    fill(Speaker, .direction = "down") %>% 
    mutate(date = if_else(str_detect(value, "^[A-Z][a-z]+\\s+\\d{1,2},\\s+\\d{4}"), 
                          value, 
                          NA)) %>% 
    fill(date, .direction = "down") %>% 
    mutate(debate_city = if_else(str_detect(value, "^Presidential Debate"), 
                                 value, 
                                 NA)) %>% 
    fill(debate_city, .direction = "down") %>% 
    filter(!is.na(Speaker)) %>% 
    mutate(date = str_extract(date, "^[A-Za-z]+\\s\\d{1,2},\\s\\d{4}")) %>%
    mutate(date = str_extract(date, "\\d{4}$")) %>%
    mutate(Speech = str_remove(value, Speaker)) %>% 
    select(-value, -cleaned) %>%
    mutate(Speech = str_trim(Speech)) %>%
    mutate(Speaker = str_remove(Speaker, ":")) %>% 
    mutate(Speech = str_replace_all(Speech, "\\[.*\\]", "")) %>% 
    mutate(Speech = na_if(Speech, "")) %>% 
    filter(!is.na(Speech))
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
