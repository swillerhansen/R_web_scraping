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
```

``` output
# Downloading packages -------------------------------------------------------
- Downloading polite from CRAN ...              OK [143.3 Kb in 0.2s]
- Downloading httr from CRAN ...                OK [473.4 Kb in 0.18s]
- Downloading curl from CRAN ...                OK [765.4 Kb in 0.19s]
- Downloading openssl from CRAN ...             OK [1.3 Mb in 0.14s]
- Downloading askpass from CRAN ...             OK [21.4 Kb in 0.13s]
- Downloading sys from CRAN ...                 OK [39.9 Kb in 0.17s]
- Downloading magrittr from CRAN ...            OK [218.7 Kb in 0.16s]
- Downloading ratelimitr from CRAN ...          OK [40.9 Kb in 0.15s]
- Downloading assertthat from CRAN ...          OK [52.1 Kb in 0.16s]
- Downloading robotstxt from CRAN ...           OK [195.5 Kb in 0.19s]
- Downloading stringr from CRAN ...             OK [303.2 Kb in 0.2s]
- Downloading stringi from CRAN ...             OK [3.1 Mb in 0.17s]
- Downloading vctrs from CRAN ...               OK [1.3 Mb in 0.19s]
- Downloading spiderbar from CRAN ...           OK [118.1 Kb in 0.15s]
- Downloading Rcpp from CRAN ...                OK [2.1 Mb in 0.15s]
- Downloading future.apply from CRAN ...        OK [153.3 Kb in 0.18s]
- Downloading future from CRAN ...              OK [641.6 Kb in 0.17s]
- Downloading globals from CRAN ...             OK [105.9 Kb in 0.13s]
- Downloading listenv from CRAN ...             OK [103.7 Kb in 0.18s]
- Downloading parallelly from CRAN ...          OK [486 Kb in 0.18s]
- Downloading rvest from CRAN ...               OK [291.8 Kb in 0.17s]
- Downloading selectr from CRAN ...             OK [487.6 Kb in 0.19s]
- Downloading tibble from CRAN ...              OK [662.7 Kb in 0.17s]
- Downloading fansi from CRAN ...               OK [310.2 Kb in 0.18s]
- Downloading pillar from CRAN ...              OK [637.8 Kb in 0.14s]
- Downloading utf8 from CRAN ...                OK [144.7 Kb in 0.17s]
- Downloading pkgconfig from CRAN ...           OK [17.8 Kb in 0.19s]
- Downloading xml2 from CRAN ...                OK [276 Kb in 0.17s]
- Downloading usethis from CRAN ...             OK [894.5 Kb in 0.18s]
- Downloading clipr from CRAN ...               OK [50.1 Kb in 0.14s]
- Downloading crayon from CRAN ...              OK [160.2 Kb in 0.17s]
- Downloading desc from CRAN ...                OK [326.6 Kb in 0.14s]
- Downloading gert from CRAN ...                OK [2.7 Mb in 0.18s]
- Downloading credentials from CRAN ...         OK [216.1 Kb in 0.13s]
- Downloading rstudioapi from CRAN ...          OK [310.3 Kb in 0.18s]
- Downloading zip from CRAN ...                 OK [621.9 Kb in 0.14s]
- Downloading gh from CRAN ...                  OK [116.5 Kb in 0.13s]
- Downloading gitcreds from CRAN ...            OK [94.9 Kb in 0.17s]
- Downloading httr2 from CRAN ...               OK [628.5 Kb in 0.17s]
- Downloading withr from CRAN ...               OK [218.4 Kb in 0.13s]
- Downloading ini from CRAN ...                 OK [13.2 Kb in 0.16s]
- Downloading purrr from CRAN ...               OK [487.8 Kb in 0.13s]
- Downloading rprojroot from CRAN ...           OK [104.7 Kb in 0.19s]
- Downloading whisker from CRAN ...             OK [65.3 Kb in 0.13s]
Successfully downloaded 44 packages in 15 seconds.

The following package(s) will be installed:
- askpass      [1.2.1]
- assertthat   [0.2.1]
- clipr        [0.8.0]
- crayon       [1.5.3]
- credentials  [2.0.2]
- curl         [6.0.1]
- desc         [1.4.3]
- fansi        [1.0.6]
- future       [1.34.0]
- future.apply [1.11.3]
- gert         [2.1.4]
- gh           [1.4.1]
- gitcreds     [0.1.2]
- globals      [0.16.3]
- httr         [1.4.7]
- httr2        [1.0.7]
- ini          [0.3.1]
- listenv      [0.9.1]
- magrittr     [2.0.3]
- openssl      [2.2.2]
- parallelly   [1.40.1]
- pillar       [1.9.0]
- pkgconfig    [2.0.3]
- polite       [0.1.3]
- purrr        [1.0.2]
- ratelimitr   [0.4.1]
- Rcpp         [1.0.13-1]
- robotstxt    [0.7.15]
- rprojroot    [2.0.4]
- rstudioapi   [0.17.1]
- rvest        [1.0.4]
- selectr      [0.4-2]
- spiderbar    [0.2.5]
- stringi      [1.8.4]
- stringr      [1.5.1]
- sys          [3.4.3]
- tibble       [3.2.1]
- usethis      [3.1.0]
- utf8         [1.2.4]
- vctrs        [0.6.5]
- whisker      [0.4.1]
- withr        [3.0.2]
- xml2         [1.3.6]
- zip          [2.3.1]
These packages will be installed into "~/work/R_web_scraping/R_web_scraping/renv/profiles/lesson-requirements/renv/library/linux-ubuntu-jammy/R-4.4/x86_64-pc-linux-gnu".

# Installing packages --------------------------------------------------------
- Installing curl ...                           OK [installed binary and cached in 0.2s]
- Installing sys ...                            OK [installed binary and cached in 0.17s]
- Installing askpass ...                        OK [installed binary and cached in 0.16s]
- Installing openssl ...                        OK [installed binary and cached in 0.22s]
- Installing httr ...                           OK [installed binary and cached in 0.18s]
- Installing magrittr ...                       OK [installed binary and cached in 0.17s]
- Installing assertthat ...                     OK [installed binary and cached in 0.16s]
- Installing ratelimitr ...                     OK [installed binary and cached in 0.16s]
- Installing stringi ...                        OK [installed binary and cached in 0.3s]
- Installing vctrs ...                          OK [installed binary and cached in 0.32s]
- Installing stringr ...                        OK [installed binary and cached in 0.32s]
- Installing Rcpp ...                           OK [installed binary and cached in 0.38s]
- Installing spiderbar ...                      OK [installed binary and cached in 0.24s]
- Installing globals ...                        OK [installed binary and cached in 0.17s]
- Installing listenv ...                        OK [installed binary and cached in 0.17s]
- Installing parallelly ...                     OK [installed binary and cached in 0.27s]
- Installing future ...                         OK [installed binary and cached in 0.23s]
- Installing future.apply ...                   OK [installed binary and cached in 0.26s]
- Installing robotstxt ...                      OK [installed binary and cached in 0.18s]
- Installing selectr ...                        OK [installed binary and cached in 0.33s]
- Installing fansi ...                          OK [installed binary and cached in 0.17s]
- Installing utf8 ...                           OK [installed binary and cached in 0.18s]
- Installing pillar ...                         OK [installed binary and cached in 0.4s]
- Installing pkgconfig ...                      OK [installed binary and cached in 0.19s]
- Installing tibble ...                         OK [installed binary and cached in 0.44s]
- Installing xml2 ...                           OK [installed binary and cached in 0.28s]
- Installing rvest ...                          OK [installed binary and cached in 0.34s]
- Installing clipr ...                          OK [installed binary and cached in 0.17s]
- Installing crayon ...                         OK [installed binary and cached in 0.17s]
- Installing desc ...                           OK [installed binary and cached in 0.17s]
- Installing credentials ...                    OK [installed binary and cached in 0.21s]
- Installing rstudioapi ...                     OK [installed binary and cached in 0.17s]
- Installing zip ...                            OK [installed binary and cached in 0.19s]
- Installing gert ...                           OK [installed binary and cached in 0.3s]
- Installing gitcreds ...                       OK [installed binary and cached in 0.17s]
- Installing withr ...                          OK [installed binary and cached in 0.17s]
- Installing httr2 ...                          OK [installed binary and cached in 0.31s]
- Installing ini ...                            OK [installed binary and cached in 0.16s]
- Installing gh ...                             OK [installed binary and cached in 0.31s]
- Installing purrr ...                          OK [installed binary and cached in 0.31s]
- Installing rprojroot ...                      OK [installed binary and cached in 0.17s]
- Installing whisker ...                        OK [installed binary and cached in 0.17s]
- Installing usethis ...                        OK [installed binary and cached in 0.37s]
- Installing polite ...                         OK [installed binary and cached in 0.4s]
Successfully installed 44 packages in 12 seconds.
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
Successfully installed 1 package in 6.3 milliseconds.
```

``` r
install.packages("tidyverse")
```

``` output
# Downloading packages -------------------------------------------------------
- Downloading tidyverse from CRAN ...           OK [415.8 Kb in 0.22s]
- Downloading broom from CRAN ...               OK [1.8 Mb in 0.18s]
- Downloading backports from CRAN ...           OK [113.4 Kb in 0.17s]
- Downloading dplyr from CRAN ...               OK [1.4 Mb in 0.14s]
- Downloading generics from CRAN ...            OK [80.9 Kb in 0.14s]
- Downloading tidyselect from CRAN ...          OK [219.9 Kb in 0.17s]
- Downloading tidyr from CRAN ...               OK [1.1 Mb in 0.2s]
- Downloading cpp11 from CRAN ...               OK [282.8 Kb in 0.19s]
- Downloading conflicted from CRAN ...          OK [53.9 Kb in 0.17s]
- Downloading dbplyr from CRAN ...              OK [1.2 Mb in 0.14s]
- Downloading blob from CRAN ...                OK [46.6 Kb in 0.18s]
- Downloading DBI from CRAN ...                 OK [894.8 Kb in 0.17s]
- Downloading dtplyr from CRAN ...              OK [349.4 Kb in 0.16s]
- Downloading data.table from CRAN ...          OK [2.3 Mb in 0.15s]
- Downloading forcats from CRAN ...             OK [411.7 Kb in 0.14s]
- Downloading ggplot2 from CRAN ...             OK [4.8 Mb in 0.16s]
- Downloading gtable from CRAN ...              OK [217.6 Kb in 0.17s]
- Downloading isoband from CRAN ...             OK [1.6 Mb in 0.18s]
- Downloading scales from CRAN ...              OK [694.8 Kb in 0.17s]
- Downloading farver from CRAN ...              OK [1.4 Mb in 0.18s]
- Downloading labeling from CRAN ...            OK [59.6 Kb in 0.13s]
- Downloading munsell from CRAN ...             OK [237.5 Kb in 0.13s]
- Downloading colorspace from CRAN ...          OK [2.5 Mb in 0.17s]
- Downloading RColorBrewer from CRAN ...        OK [50.8 Kb in 0.13s]
- Downloading viridisLite from CRAN ...         OK [1.2 Mb in 0.18s]
- Downloading googledrive from CRAN ...         OK [1.8 Mb in 0.19s]
- Downloading gargle from CRAN ...              OK [755.6 Kb in 0.16s]
- Downloading uuid from CRAN ...                OK [47.6 Kb in 0.17s]
- Downloading googlesheets4 from CRAN ...       OK [505.1 Kb in 0.14s]
- Downloading cellranger from CRAN ...          OK [101.3 Kb in 0.17s]
- Downloading rematch from CRAN ...             OK [15.9 Kb in 0.17s]
- Downloading ids from CRAN ...                 OK [119.8 Kb in 0.17s]
- Downloading rematch2 from CRAN ...            OK [45 Kb in 0.16s]
- Downloading haven from CRAN ...               OK [375 Kb in 0.16s]
- Downloading hms from CRAN ...                 OK [97.8 Kb in 0.17s]
- Downloading readr from CRAN ...               OK [836.4 Kb in 0.2s]
- Downloading vroom from CRAN ...               OK [925.4 Kb in 0.13s]
- Downloading bit64 from CRAN ...               OK [482 Kb in 0.17s]
- Downloading bit from CRAN ...                 OK [1.1 Mb in 0.17s]
- Downloading tzdb from CRAN ...                OK [626.5 Kb in 0.14s]
- Downloading progress from CRAN ...            OK [84.4 Kb in 0.13s]
- Downloading prettyunits from CRAN ...         OK [149.1 Kb in 0.16s]
- Downloading lubridate from CRAN ...           OK [962.5 Kb in 0.17s]
- Downloading timechange from CRAN ...          OK [166.7 Kb in 0.17s]
- Downloading modelr from CRAN ...              OK [195.9 Kb in 0.17s]
- Downloading ragg from CRAN ...                OK [629.8 Kb in 0.13s]
- Downloading systemfonts from CRAN ...         OK [254.8 Kb in 0.18s]
- Downloading textshaping from CRAN ...         OK [121 Kb in 0.19s]
- Downloading readxl from CRAN ...              OK [844.4 Kb in 0.18s]
- Downloading reprex from CRAN ...              OK [483.4 Kb in 0.17s]
- Downloading callr from CRAN ...               OK [435 Kb in 0.18s]
- Downloading processx from CRAN ...            OK [434.1 Kb in 0.15s]
- Downloading ps from CRAN ...                  OK [475.1 Kb in 0.19s]
Successfully downloaded 53 packages in 17 seconds.

The following package(s) will be installed:
- backports     [1.5.0]
- bit           [4.5.0.1]
- bit64         [4.5.2]
- blob          [1.2.4]
- broom         [1.0.7]
- callr         [3.7.6]
- cellranger    [1.1.0]
- colorspace    [2.1-1]
- conflicted    [1.2.0]
- cpp11         [0.5.1]
- data.table    [1.16.4]
- DBI           [1.2.3]
- dbplyr        [2.5.0]
- dplyr         [1.1.4]
- dtplyr        [1.3.1]
- farver        [2.1.2]
- forcats       [1.0.0]
- gargle        [1.5.2]
- generics      [0.1.3]
- ggplot2       [3.5.1]
- googledrive   [2.1.1]
- googlesheets4 [1.1.1]
- gtable        [0.3.6]
- haven         [2.5.4]
- hms           [1.1.3]
- ids           [1.0.1]
- isoband       [0.2.7]
- labeling      [0.4.3]
- lubridate     [1.9.4]
- modelr        [0.1.11]
- munsell       [0.5.1]
- prettyunits   [1.2.0]
- processx      [3.8.4]
- progress      [1.2.3]
- ps            [1.8.1]
- ragg          [1.3.3]
- RColorBrewer  [1.1-3]
- readr         [2.1.5]
- readxl        [1.4.3]
- rematch       [2.0.0]
- rematch2      [2.1.2]
- reprex        [2.1.1]
- scales        [1.3.0]
- systemfonts   [1.1.0]
- textshaping   [0.4.1]
- tidyr         [1.3.1]
- tidyselect    [1.2.1]
- tidyverse     [2.0.0]
- timechange    [0.3.0]
- tzdb          [0.4.0]
- uuid          [1.2-1]
- viridisLite   [0.4.2]
- vroom         [1.6.5]
These packages will be installed into "~/work/R_web_scraping/R_web_scraping/renv/profiles/lesson-requirements/renv/library/linux-ubuntu-jammy/R-4.4/x86_64-pc-linux-gnu".

# Installing packages --------------------------------------------------------
- Installing backports ...                      OK [installed binary and cached in 0.17s]
- Installing generics ...                       OK [installed binary and cached in 0.16s]
- Installing tidyselect ...                     OK [installed binary and cached in 0.3s]
- Installing dplyr ...                          OK [installed binary and cached in 0.5s]
- Installing cpp11 ...                          OK [installed binary and cached in 0.18s]
- Installing tidyr ...                          OK [installed binary and cached in 0.51s]
- Installing broom ...                          OK [installed binary and cached in 0.54s]
- Installing conflicted ...                     OK [installed binary and cached in 0.28s]
- Installing blob ...                           OK [installed binary and cached in 0.29s]
- Installing DBI ...                            OK [installed binary and cached in 0.27s]
- Installing dbplyr ...                         OK [installed binary and cached in 0.74s]
- Installing data.table ...                     OK [installed binary and cached in 0.28s]
- Installing dtplyr ...                         OK [installed binary and cached in 0.53s]
- Installing forcats ...                        OK [installed binary and cached in 0.3s]
- Installing gtable ...                         OK [installed binary and cached in 0.42s]
- Installing isoband ...                        OK [installed binary and cached in 0.21s]
- Installing farver ...                         OK [installed binary and cached in 0.2s]
- Installing labeling ...                       OK [installed binary and cached in 0.17s]
- Installing colorspace ...                     OK [installed binary and cached in 0.27s]
- Installing munsell ...                        OK [installed binary and cached in 0.21s]
- Installing RColorBrewer ...                   OK [installed binary and cached in 0.16s]
- Installing viridisLite ...                    OK [installed binary and cached in 0.17s]
- Installing scales ...                         OK [installed binary and cached in 0.34s]
- Installing ggplot2 ...                        OK [installed binary and cached in 0.68s]
- Installing gargle ...                         OK [installed binary and cached in 0.33s]
- Installing uuid ...                           OK [installed binary and cached in 0.16s]
- Installing googledrive ...                    OK [installed binary and cached in 0.55s]
- Installing rematch ...                        OK [installed binary and cached in 0.17s]
- Installing cellranger ...                     OK [installed binary and cached in 0.16s]
- Installing ids ...                            OK [installed binary and cached in 0.16s]
- Installing rematch2 ...                       OK [installed binary and cached in 0.39s]
- Installing googlesheets4 ...                  OK [installed binary and cached in 0.55s]
- Installing hms ...                            OK [installed binary and cached in 0.28s]
- Installing bit ...                            OK [installed binary and cached in 0.18s]
- Installing bit64 ...                          OK [installed binary and cached in 0.19s]
- Installing tzdb ...                           OK [installed binary and cached in 0.2s]
- Installing prettyunits ...                    OK [installed binary and cached in 0.16s]
- Installing progress ...                       OK [installed binary and cached in 0.29s]
- Installing vroom ...                          OK [installed binary and cached in 0.48s]
- Installing readr ...                          OK [installed binary and cached in 0.43s]
- Installing haven ...                          OK [installed binary and cached in 0.44s]
- Installing timechange ...                     OK [installed binary and cached in 0.17s]
- Installing lubridate ...                      OK [installed binary and cached in 0.29s]
- Installing modelr ...                         OK [installed binary and cached in 0.49s]
- Installing systemfonts ...                    OK [installed binary and cached in 0.27s]
- Installing textshaping ...                    OK [installed binary and cached in 0.27s]
- Installing ragg ...                           OK [installed binary and cached in 0.29s]
- Installing readxl ...                         OK [installed binary and cached in 0.19s]
- Installing ps ...                             OK [installed binary and cached in 0.17s]
- Installing processx ...                       OK [installed binary and cached in 0.18s]
- Installing callr ...                          OK [installed binary and cached in 0.19s]
- Installing reprex ...                         OK [installed binary and cached in 0.32s]
- Installing tidyverse ...                      OK [installed binary and cached in 0.17s]
Successfully installed 53 packages in 17 seconds.
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
Successfully installed 1 package in 7.3 milliseconds.
```

``` r
install.packages("htmlTable")
```

``` output
# Downloading packages -------------------------------------------------------
- Downloading htmlTable from CRAN ...           OK [408 Kb in 0.13s]
- Downloading checkmate from CRAN ...           OK [726.5 Kb in 0.18s]
- Downloading htmlwidgets from CRAN ...         OK [796.3 Kb in 0.18s]
Successfully downloaded 3 packages in 1 second.

The following package(s) will be installed:
- checkmate   [2.3.2]
- htmlTable   [2.4.3]
- htmlwidgets [1.6.4]
These packages will be installed into "~/work/R_web_scraping/R_web_scraping/renv/profiles/lesson-requirements/renv/library/linux-ubuntu-jammy/R-4.4/x86_64-pc-linux-gnu".

# Installing packages --------------------------------------------------------
- Installing checkmate ...                      OK [installed binary and cached in 0.18s]
- Installing htmlwidgets ...                    OK [installed binary and cached in 0.29s]
- Installing htmlTable ...                      OK [installed binary and cached in 0.38s]
Successfully installed 3 packages in 0.94 seconds.
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
Successfully installed 1 package in 7.6 milliseconds.
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
Successfully installed 1 package in 7.8 milliseconds.
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

