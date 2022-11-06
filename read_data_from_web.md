read_data_from_web
================
Jiayi Shi
2022-10-13

## NSDUH data

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"
drug_use_html = read_html(url)

drug_use_html
```

    ## {html_document}
    ## <html lang="en">
    ## [1] <head>\n<link rel="P3Pv1" href="http://www.samhsa.gov/w3c/p3p.xml">\n<tit ...
    ## [2] <body>\r\n\r\n<noscript>\r\n<p>Your browser's Javascript is off. Hyperlin ...

``` r
drug_use_html %>%
  html_table() %>% # get all the tables from the website
  first() %>% 
  slice(-1) # get rid off the first row
```

    ## # A tibble: 56 × 16
    ##    State 12+(2…¹ 12+(2…² 12+(P…³ 12-17…⁴ 12-17…⁵ 12-17…⁶ 18-25…⁷ 18-25…⁸ 18-25…⁹
    ##    <chr> <chr>   <chr>   <chr>   <chr>   <chr>   <chr>   <chr>   <chr>   <chr>  
    ##  1 Tota… 12.90a  13.36   0.002   13.28b  12.86   0.063   31.78   32.07   0.369  
    ##  2 Nort… 13.88a  14.66   0.005   13.98   13.51   0.266   34.66a  36.45   0.008  
    ##  3 Midw… 12.40b  12.76   0.082   12.45   12.33   0.726   32.13   32.20   0.900  
    ##  4 South 11.24a  11.64   0.029   12.02   11.88   0.666   28.93   29.20   0.581  
    ##  5 West  15.27   15.62   0.262   15.53a  14.43   0.018   33.72   33.19   0.460  
    ##  6 Alab… 9.98    9.60    0.426   9.90    9.71    0.829   26.99   26.13   0.569  
    ##  7 Alas… 19.60a  21.92   0.010   17.30   18.44   0.392   36.47a  40.69   0.015  
    ##  8 Ariz… 13.69   13.12   0.364   15.12   13.45   0.131   31.53   31.15   0.826  
    ##  9 Arka… 11.37   11.59   0.678   12.79   12.14   0.538   26.53   27.06   0.730  
    ## 10 Cali… 14.49   15.25   0.103   15.03   14.11   0.190   33.69   32.72   0.357  
    ## # … with 46 more rows, 6 more variables: `26+(2013-2014)` <chr>,
    ## #   `26+(2014-2015)` <chr>, `26+(P Value)` <chr>, `18+(2013-2014)` <chr>,
    ## #   `18+(2014-2015)` <chr>, `18+(P Value)` <chr>, and abbreviated variable
    ## #   names ¹​`12+(2013-2014)`, ²​`12+(2014-2015)`, ³​`12+(P Value)`,
    ## #   ⁴​`12-17(2013-2014)`, ⁵​`12-17(2014-2015)`, ⁶​`12-17(P Value)`,
    ## #   ⁷​`18-25(2013-2014)`, ⁸​`18-25(2014-2015)`, ⁹​`18-25(P Value)`

### Assessment

``` r
cost_living_html = read_html("https://www.bestplaces.net/cost_of_living/city/new_york/new_york") %>% 
  html_table(header = T) %>% # First row be header
  first()
```

## StarWars data

``` r
swm_html = 
  read_html("https://www.imdb.com/list/ls070150896/")
```

### Get the staff I want

``` r
title_vec = 
  swm_html %>%
  html_elements(".lister-item-header a") %>%
  html_text()

gross_rev_vec = 
  swm_html %>%
  html_elements(".text-small:nth-child(7) span:nth-child(5)") %>%
  html_text()

runtime_vec = 
  swm_html %>%
  html_elements(".runtime") %>%
  html_text()

swm_df = 
  tibble(
    title = title_vec,
    rev = gross_rev_vec,
    runtime = runtime_vec)
```

### Amazon reviews

``` r
url = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber=1"

nd_html = read_html(url)

review_titles = nd_html %>% 
  html_elements(".a-text-bold span") %>% 
  html_text()

review_stars = nd_html %>% 
  html_elements("#cm_cr-review_list .review-rating") %>% 
  html_text()

review_text = nd_html %>% 
  html_elements(".review-text-content span") %>% 
  html_text()

review_df = tibble(
  title = review_titles,
  stars = review_stars,
  text = review_text
)

review_df
```

    ## # A tibble: 10 × 3
    ##    title                                      stars              text           
    ##    <chr>                                      <chr>              <chr>          
    ##  1 Lol hey it’s Napoleon. What’s not to love… 5.0 out of 5 stars "Vote for Pedr…
    ##  2 Still the best                             5.0 out of 5 stars "Completely st…
    ##  3 70’s and 80’s Schtick Comedy               5.0 out of 5 stars "…especially f…
    ##  4 Amazon Censorship                          5.0 out of 5 stars "I hope Amazon…
    ##  5 Watch to say you did                       3.0 out of 5 stars "I know it's s…
    ##  6 Best Movie Ever!                           5.0 out of 5 stars "We just love …
    ##  7 Quirky                                     5.0 out of 5 stars "Good family f…
    ##  8 Funny movie - can't play it !              1.0 out of 5 stars "Sony 4k playe…
    ##  9 A brilliant story about teenage life       5.0 out of 5 stars "Napoleon Dyna…
    ## 10 HUHYAH                                     5.0 out of 5 stars "Spicy"

## APIs

CSV file:

``` r
water_df = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") %>%  # OpenDate dataset API CSV (JSON difficult to operate)
  content("parsed")
```

    ## Rows: 43 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (4): year, new_york_city_population, nyc_consumption_million_gallons_per...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

JSON file:

``` r
nyc_water = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.json") %>% 
  content("text") %>%
  jsonlite::fromJSON() %>%
  as_tibble()
```

By default, the CDC API limits data to the first 1000 rows. Here I’ve
increased that by changing an element of the API query – I looked around
the website describing the API to find the name of the argument, and
then used the appropriate syntax for GET. To get the full data, I could
increase this so that I get all the data at once or I could try
iterating over chunks of a few thousand rows.

``` r
brfss_smart2010 = 
  GET("https://chronicdata.cdc.gov/resource/acme-vg9e.csv",
      query = list("$limit" = 5000)) %>% 
  content("parsed")
```

    ## Rows: 5000 Columns: 23
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (16): locationabbr, locationdesc, class, topic, question, response, data...
    ## dbl  (6): year, sample_size, data_value, confidence_limit_low, confidence_li...
    ## lgl  (1): locationid
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
poke = 
  GET("http://pokeapi.co/api/v2/pokemon/1") %>%
  content() # 通常先写“parsed”看看能不能guess，使之更好读
```

## Alzheimer’s Disease and Healthy Aging Data

``` r
url = "https://chronicdata.cdc.gov/resource/hfr9-rurv.csv"

alz_html = 
  GET(url,
      query = list("$limit" = 5000)) %>%
  content("parsed")
```

    ## Rows: 5000 Columns: 39
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (24): locationabbr, locationdesc, datasource, class, topic, question, da...
    ## dbl  (6): yearstart, yearend, data_value, data_value_alt, low_confidence_lim...
    ## lgl  (9): rowid, response, sample_size, stratificationcategory3, stratificat...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
