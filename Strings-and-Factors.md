Strings and Factors
================
Lectured by Jeff Goldsmith
2022-10-18

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6     ✔ purrr   0.3.4
    ## ✔ tibble  3.1.8     ✔ dplyr   1.0.9
    ## ✔ tidyr   1.2.1     ✔ stringr 1.4.1
    ## ✔ readr   2.1.2     ✔ forcats 0.5.2
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## 
    ## 载入程辑包：'rvest'
    ## 
    ## 
    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

# Strings and regex

You can use `str_detect` to find cases where the match exists (often
useful in conjunction with `filter`), and you can use `str_replace` to
replace an instance of a match with something else (often useful in
conjunction with `mutate`).

In the following examples we’ll mostly focus on vectors to avoid the
complication of data frames, but we’ll see those shortly.

``` r
string_vec = c("my", "name", "is", "jeff")

str_detect(string_vec, "jeff")
```

    ## [1] FALSE FALSE FALSE  TRUE
