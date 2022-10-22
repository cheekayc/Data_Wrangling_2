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
# Let us define a collection of string vectors, they will be presented in dataframe:
string_vec = c("my", "name", "is", "jeff")

# Detect this pattern "jeff" in the string_vec:
str_detect(string_vec, "jeff")
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
# Detect "a" in the string_vec:
str_detect(string_vec, "a")
```

    ## [1] FALSE  TRUE FALSE FALSE

``` r
# "a" is only appeared in the second string "name".

# Detect "m":
str_detect(string_vec, "m")
```

    ## [1]  TRUE  TRUE FALSE FALSE

``` r
# "m" is appeared in the first two strings "my" & "name".
```

``` r
# Replace the string "jeff" with "Jeff":
str_replace(string_vec, "jeff", "Jeff")
```

    ## [1] "my"   "name" "is"   "Jeff"

``` r
# Replace lowercase "m" with uppercase "M":
str_replace(string_vec, "m", "M")
```

    ## [1] "My"   "naMe" "is"   "jeff"

For exact matches, we can designate matches at the beginning or end of a
line.

``` r
string_vec2 = c(
  "i think we all rule for participating",
  "i think i have been caught",
  "i think this will be quite fun actually",
  "it will be fun, i think")

str_detect(string_vec2, "i think")
```

    ## [1] TRUE TRUE TRUE TRUE

``` r
# It will return "TRUE" for all because "i think" appears in all four strings.

# Detect something that starts with "i think":
str_detect(string_vec2, "^i think")
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
# Detect something that ends with "i think":
str_detect(string_vec2, "i think$")
```

    ## [1] FALSE FALSE FALSE  TRUE

In the following example, we see “Bush” in different forms
(upper/lowercase, standalone word or connected with other words). We can
still detect all of them:

``` r
string_vec3 = c(
  "Y'all remember Pres. HW Bush?",
  "I saw a green bush",
  "BBQ and Bushwalking at Molonglo Gorge",
  "BUSH -- LIVE IN CONCERT!!")

str_detect(string_vec3,"[Bb]ush")
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
# The all uppercase BUSH is not detected.

# This will allow me to detect all forms of "bush".
str_detect(string_vec3, "[Bb][Uu][Ss][Hh]")
```

    ## [1] TRUE TRUE TRUE TRUE

Which of these following vectors have numbers followed by letters?

``` r
string_vec4 = c(
  '7th inning stretch',
  '1st half soon to begin. Texas won the toss.',
  'she is 5 feet 4 inches tall',
  '3AM - cant sleep :(')

# Detect anything that has a number from 0 to 9, followed by a letter from capital A to Z:
str_detect(string_vec4, "[0-9][A-Z]")
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
# Detect anything that has a number from 0 to 9, followed by a letter from lowercase a to z or capital A to Z:
str_detect(string_vec4, "[0-9][a-zA-Z]")
```

    ## [1]  TRUE  TRUE FALSE  TRUE

The character `.` matches anything.

``` r
string_vec5 = c(
  'Its 7:11 in the evening',
  'want to go to 7-11?',
  'my flight is AA711',
  'NetBios: scanning ip 203.167.114.66')

str_detect(string_vec5, "7.11")
```

    ## [1]  TRUE  TRUE FALSE  TRUE

``` r
# The third string wasn't detected because there isn't anything between 7 & 11.
```

What if I want to search for something exactly like numbers in the \[\]?
Some characters are “special”. These include `[` and `]`, `(` and `)`,
and `.`. If we want to search for these, we have to indicate they’re
special using `\`. Unfortunately, `\` is also special, so things get
weird.

``` r
string_vec6 = c(
  'The CI is [2, 5]',
  ':-]',
  ':-[',
  'I found the answer on pages [6-7]')

# str_detect(string_vec6, "[")
# This is not going to work because R thinks you forgot to close the bracket.

# If we want to look for exactly this "[]" in the strings, we need a "\" to indicate the bracket is special. 
# But because the "\" is also a special character, we need two "\" to make this work:
str_detect(string_vec6, "\\[")
```

    ## [1]  TRUE FALSE  TRUE  TRUE

``` r
# Detect something that has a bracket "[" followed by a range of numbers 0 to 9:
str_detect(string_vec6, "\\[[0-9]")
```

    ## [1]  TRUE FALSE FALSE  TRUE

# Why factors are weird

``` r
factor_sex = factor(c("male", "male", "female", "female"))
# R tells us there are 2 levels of factors: female male. The order of female is ahead of male, so female is 1 and male is 2.

# If I make the factor vector numeric, it will give me 2 2 1 1:
as.numeric(factor_sex)
```

    ## [1] 2 2 1 1

``` r
# I can relevel my factor vector:
factor_sex = fct_relevel(factor_sex, "male") # I am telling R to make the order of "male" before "female".

# Now if I do as.numeric I should see 1 1 2 2.
as.numeric(factor_sex)
```

    ## [1] 1 1 2 2

# Let’s do some practice!

## *NSDUH*

``` r
NSDUH = read_html("http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm") %>% 
  html_table() %>% 
  first() %>% 
  slice(-1)
```

There are a few steps we need to implement to tidy these data. For now
I’m not interested in the p-values (I’d rather just see the data); we
also have age groups and year ranges in the same column titles, both of
which are, in fact, variables. Lastly, the table includes letters as
superscripts next to table entries; if we only want the percents we’ll
need to strip these out.

``` r
NSDUH_marj = 
  NSDUH %>%
  select(-contains("P Value")) %>%
  pivot_longer(
    -State,
    names_to = "age_year", 
    values_to = "percent") %>%
# If we right away change "percent" into numeric variable, the rows that contain numbers followed by a letter will be forced to become "NA" because R don't know what to do with them.
# So we want to get rid of the letters first before we change the variable to numeric. 
  mutate(
# I want to detect any strings that has letters at the end which range from a to c, and replace them with nothing.
    percent = str_replace(percent, "[a-c]$", ""),
    percent = as.numeric(percent)) %>% 
# We want to separate "age" "year" by the opened parentheses ( but ( are special character, so we need "\" to indicate that.
  separate(age_year, into = c("age", "year"), sep = "\\(") %>%
# But now we have a closed parentheses ) hanging in the end of the strings for year, and we need to remove that.
  mutate(
# Replace the ")" with nothing
    year = str_replace(year, "\\)", "")) %>%
# I just want to look at each 50 states, not Total U.S., Northeast, etc.
  filter(!(State %in% c("Total U.S.", "Northeast", "Midwest", "South", "West", "District of Columbia")))
```

Let’s quickly visualize these data for the *12-17* age group; to make
the plot readable, we’ll treat `State` as a factor and are reordered
according to the median `percent` value.

``` r
NSDUH_marj %>%
  filter(age == "12-17") %>% 
# I want to reorder different states by ascending order in "percent" variable.
  mutate(State = fct_reorder(State, percent)) %>% 
  ggplot(aes(x = State, y = percent, color = year)) + 
    geom_point() + 
  labs(
    title = "Percentage of marijuana use among 12-17 age group") +
    theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))
```

![](Strings-and-Factors_files/figure-gfm/calculate%20median%20and%20plot-1.png)<!-- -->

``` r
# vjust adjust the position of the axis. If I use vjust = 2, my x-axis will move 2 clicks to the right.
# hjust adjust the up-down position of the axis. If I do hjust = 2, my x-axis will move 2 clicks up and overlap with my plot.
```

## *Restaurant Inspection*

The `Restaurant Inspection` dataset is inside the `p8105.datasets`
package.

``` r
data("rest_inspec")
# There is about 400,000 rows in this dataset
rest_inspec %>% 
  group_by(boro, grade) %>% 
  summarize(n = n()) %>% 
  pivot_wider(
    names_from = grade, 
    values_from = n)
```

    ## # A tibble: 6 × 8
    ## # Groups:   boro [6]
    ##   boro              A     B     C `Not Yet Graded`     P     Z  `NA`
    ##   <chr>         <int> <int> <int>            <int> <int> <int> <int>
    ## 1 BRONX         13688  2801   701              200   163   351 16833
    ## 2 BROOKLYN      37449  6651  1684              702   416   977 51930
    ## 3 MANHATTAN     61608 10532  2689              765   508  1237 80615
    ## 4 Missing           4    NA    NA               NA    NA    NA    13
    ## 5 QUEENS        35952  6492  1593              604   331   913 45816
    ## 6 STATEN ISLAND  5215   933   207               85    47   149  6730

To simplify things, I’ll remove inspections with scores other than A, B,
or C, and also remove the restaurants with missing `boro` information.
I’ll also clean up `boro` names a bit.

``` r
nyc_rest_insp = 
  rest_inspec %>% 
  filter(grade %in% c("A", "B", "C"), boro != "Missing") %>% 
  mutate(boro = str_to_title(boro)) # Instead of making all letters lowercase, str_to_title keep the first letter uppercase.

nyc_rest_insp %>% 
  group_by(boro, grade) %>% 
  summarize(n = n()) %>% 
  pivot_wider(
    names_from = grade, 
    values_from = n)
```

    ## # A tibble: 5 × 4
    ## # Groups:   boro [5]
    ##   boro              A     B     C
    ##   <chr>         <int> <int> <int>
    ## 1 Bronx         13688  2801   701
    ## 2 Brooklyn      37449  6651  1684
    ## 3 Manhattan     61608 10532  2689
    ## 4 Queens        35952  6492  1593
    ## 5 Staten Island  5215   933   207

Let’s find how many pizza places are there in NYC.

``` r
nyc_rest_insp %>% 
  filter(str_detect(dba, "[Pp][Ii][Zz][Zz][Aa]")) %>% 
  group_by(boro) %>% 
  count() # summarize(n = n()) will give the same results
```

    ## # A tibble: 5 × 2
    ## # Groups:   boro [5]
    ##   boro              n
    ##   <chr>         <int>
    ## 1 Bronx          1531
    ## 2 Brooklyn       2305
    ## 3 Manhattan      2479
    ## 4 Queens         1954
    ## 5 Staten Island   471

``` r
nyc_rest_insp %>% 
  filter(str_detect(dba, "[Pp][Ii][Zz][Zz][Aa]")) %>% 
  mutate(
    boro = str_replace(boro, "Manhattan", "The City"), # str_replace allow me to change "Manhattan" into "The City".
    # Can also do: boro = fct_recode(boro, "The City" = "Manhattan")
    boro = fct_infreq(boro)) %>% # I can rearrange the boro in terms of highest frequency to lowest frequency.)  
  ggplot(aes(x = boro)) +
  geom_bar()
```

![](Strings-and-Factors_files/figure-gfm/pizza%20time-1.png)<!-- -->

What about examining pizza places and their grades by borough?

``` r
nyc_rest_insp %>% 
  filter(str_detect(dba, "[Pp][Ii][Zz][Zz][Aa]")) %>% 
  group_by(boro, grade) %>% 
  summarize(n = n()) %>% 
  pivot_wider(names_from = grade, values_from = n)
```

    ## # A tibble: 5 × 4
    ## # Groups:   boro [5]
    ##   boro              A     B     C
    ##   <chr>         <int> <int> <int>
    ## 1 Bronx          1170   305    56
    ## 2 Brooklyn       1948   296    61
    ## 3 Manhattan      1983   420    76
    ## 4 Queens         1647   259    48
    ## 5 Staten Island   323   127    21

``` r
nyc_rest_insp %>% 
  filter(str_detect(dba, "[Pp][Ii][Zz][Zz][Aa]")) %>%
  mutate(boro = fct_infreq(boro)) %>%
  ggplot(aes(x = boro, fill = grade)) + 
  geom_bar() 
```

![](Strings-and-Factors_files/figure-gfm/pizza%20grades-1.png)<!-- -->

## *Weather Data*

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

Our first example reordered name “by hand” using `fct_relevel`:

``` r
weather_df %>%
  mutate(name = forcats::fct_relevel(name, c("Waikiki_HA", "CentralPark_NY", "Waterhole_WA"))) %>% 
  ggplot(aes(x = name, y = tmax)) + 
  geom_violin(aes(fill = name), color = "blue", alpha = .5) + 
  theme(legend.position = "bottom")
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

![](Strings-and-Factors_files/figure-gfm/hand%20reorder-1.png)<!-- -->

We could instead reorder name according to tmax values in each name
using `fct_reorder`:

``` r
weather_df %>%
  mutate(name = forcats::fct_reorder(name, tmax)) %>% 
  ggplot(aes(x = name, y = tmax)) + 
  geom_violin(aes(fill = name), color = "blue", alpha = .5) + 
  theme(legend.position = "bottom")
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

![](Strings-and-Factors_files/figure-gfm/reorder%20by%20tmax-1.png)<!-- -->

#### **Linear regression**

The ordering of factor variables is important in linear regression.
Specifically, the ordering determines the “reference” category, and is
something that can be adjusted as needed.

``` r
weather_df %>%
  lm(tmax ~ name, data = .) # Since we are piping, "data = . " tells R the tibble it should be using is the one that just piped in.
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ name, data = .)
    ## 
    ## Coefficients:
    ##      (Intercept)    nameWaikiki_HA  nameWaterhole_WA  
    ##           17.366            12.291            -9.884

``` r
weather_df %>%
  mutate(name = forcats::fct_relevel(name, c("Waikiki_HA", "CentralPark_NY", "Waterhole_WA"))) %>% 
  lm(tmax ~ name, data = .)
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ name, data = .)
    ## 
    ## Coefficients:
    ##        (Intercept)  nameCentralPark_NY    nameWaterhole_WA  
    ##              29.66              -12.29              -22.18

## *PULSE Data*

Using `stringr` and `forcats`, the result is the same as we did many
lectures before, and the differences are pretty small, but this is a bit
cleaner.

``` r
pulse_data = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>%
  janitor::clean_names() %>%
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit", 
    names_prefix = "bdi_score_",
    values_to = "bdi") %>%
  select(id, visit, everything()) %>%
  mutate(
    visit = str_replace(visit, "bl", "00m"),
    visit = factor(visit)) %>%
  arrange(id, visit)

print(pulse_data, n = 12)
```

## **Airbnb**

We could also use factors in an exploratory analysis of the Airbnb data,
for example when looking at the distribution of prices in various
neighborhoods. Ordering these according to the median price makes for
clearer plots than ordering neighborhoods alphabetically.

``` r
data("nyc_airbnb")

nyc_airbnb %>%
  filter(neighbourhood_group == "Manhattan") %>% 
  mutate(
    neighbourhood = fct_reorder(neighbourhood, price)) %>% 
  ggplot(aes(x = neighbourhood, y = price)) +
  geom_boxplot() +
  coord_flip() + 
  ylim(0, 1000)
```

    ## Warning: Removed 109 rows containing non-finite values (stat_boxplot).

![](Strings-and-Factors_files/figure-gfm/airbnb-1.png)<!-- -->
