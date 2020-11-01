dw and interactivity
================
Liucheng Shi

``` r
library(tidyverse)
```

    ## Warning: package 'tibble' was built under R version 4.0.3

    ## Warning: package 'readr' was built under R version 4.0.3

``` r
library(rvest)
library(httr)

knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)
theme_set(theme_minimal() + theme(legend.position = "bottom"))
options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)
scale_colour_discrete = scale_colour_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

## Scrape a table

The first table from [this
page](http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm)

read in the html

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"


drug_us_html = read_html(url)
drug_us_html
```

    ## {html_document}
    ## <html lang="en">
    ## [1] <head>\n<link rel="P3Pv1" href="http://www.samhsa.gov/w3c/p3p.xml">\n<tit ...
    ## [2] <body>\r\n\r\n<noscript>\r\n<p>Your browser's Javascript is off. Hyperlin ...

extract the table(s); focus on the first one

``` r
tabl_marj = 
  drug_us_html %>% 
  html_nodes(css = "table") %>% 
  first() %>% 
  html_table() %>%
  slice(-1) %>% 
  as_tibble()
```

## star wars movie info

the data from [here](https://www.imdb.com/list/ls070150896/).

``` r
url = "https://www.imdb.com/list/ls070150896/"

swm_html = read_html(url)
```

Grab elements

``` r
title_vec = 
  swm_html %>% 
  html_nodes(css = ".lister-item-header a") %>% 
  html_text()

gross_rev_vec =
  swm_html %>% 
  html_nodes(css = ".text-muted .ghost~ .text-muted+ span") %>% 
  html_text()

runtime_vec = 
  swm_html %>% 
  html_nodes(css = ".runtime") %>% 
  html_text()

swm_df = tibble(
  title = title_vec,
  gross_rev = gross_rev_vec,
  runtime = runtime_vec
)
```

## Get some water data

``` r
nyc_water = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") %>% 
  content("parsed")
```

    ## 
    ## -- Column specification --------------------------------------------------------
    ## cols(
    ##   year = col_double(),
    ##   new_york_city_population = col_double(),
    ##   nyc_consumption_million_gallons_per_day = col_double(),
    ##   per_capita_gallons_per_person_per_day = col_double()
    ## )

``` r
nyc_water_json = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.json") %>% 
  content("text") %>% 
  jsonlite::fromJSON() %>% 
  as_tibble()
```

## BRFSS

``` r
brfss_2010 = 
  GET("https://chronicdata.cdc.gov/resource/acme-vg9e.csv",
      query = list("$limit" = 5000)) %>% 
  content("parsed")
```

    ## 
    ## -- Column specification --------------------------------------------------------
    ## cols(
    ##   .default = col_character(),
    ##   year = col_double(),
    ##   sample_size = col_double(),
    ##   data_value = col_double(),
    ##   confidence_limit_low = col_double(),
    ##   confidence_limit_high = col_double(),
    ##   display_order = col_double(),
    ##   locationid = col_logical()
    ## )
    ## i Use `spec()` for the full column specifications.

## Let’s look at Pokemon\!

``` r
pokemon_data = 
  GET("https://pokeapi.co/api/v2/pokemon/1") %>% 
  content

pokemon_data$name
```

    ## [1] "bulbasaur"

``` r
pokemon_data$height
```

    ## [1] 7

``` r
pokemon_data$ability
```

    ## NULL
