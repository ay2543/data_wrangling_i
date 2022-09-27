Data Manipulation
================
2022-09-22

Once you’ve imported data, you’re going to need to do some cleaning up.

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.0      ✔ stringr 1.4.1 
    ## ✔ readr   2.1.2      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
options(tibble.print_min = 3) #print the first 3 rows

litters_data = read_csv("./data/FAS_litters.csv",
  col_types = "ccddiiii")
litters_data = janitor::clean_names(litters_data)

pups_data = read_csv("./data/FAS_pups.csv",
  col_types = "ciiiii")
pups_data = janitor::clean_names(pups_data)
```

## `select`

You can use the `select` function to specify the columns you want.

You can select which columns you want to keep by naming all of them:

``` r
select(litters_data, group, litter_number, gd0_weight, pups_born_alive)
```

    ## # A tibble: 49 × 4
    ##   group litter_number gd0_weight pups_born_alive
    ##   <chr> <chr>              <dbl>           <int>
    ## 1 Con7  #85                 19.7               3
    ## 2 Con7  #1/2/95/2           27                 8
    ## 3 Con7  #5/5/3/83/3-3       26                 6
    ## # … with 46 more rows

This tends to be tedious, so there are some shortcuts.

You can specify a range of variables to include:

``` r
select(litters_data, group:gd_of_birth) 
```

    ## # A tibble: 49 × 5
    ##   group litter_number gd0_weight gd18_weight gd_of_birth
    ##   <chr> <chr>              <dbl>       <dbl>       <int>
    ## 1 Con7  #85                 19.7        34.7          20
    ## 2 Con7  #1/2/95/2           27          42            19
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19
    ## # … with 46 more rows

``` r
# Select variables from group to gd_of_birth
```

If you need many variables, you can specify which columns you’d like to
remove:

``` r
select(litters_data, -pups_survive)
```

    ## # A tibble: 49 × 7
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive pups_…¹
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20               3       4
    ## 2 Con7  #1/2/95/2           27          42            19               8       0
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6       0
    ## # … with 46 more rows, and abbreviated variable name ¹​pups_dead_birth

``` r
# Include all variables except for pups_survive (minus sign)
select(litters_data, -pups_survive, -group)
```

    ## # A tibble: 49 × 6
    ##   litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive pups_dead_b…¹
    ##   <chr>              <dbl>       <dbl>       <int>           <int>         <int>
    ## 1 #85                 19.7        34.7          20               3             4
    ## 2 #1/2/95/2           27          42            19               8             0
    ## 3 #5/5/3/83/3-3       26          41.4          19               6             0
    ## # … with 46 more rows, and abbreviated variable name ¹​pups_dead_birth

You can also use the `select` function to rename variables as you pull
them:

``` r
# new name on the left, original variable on the right of the equal sign
select(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
```

    ## # A tibble: 49 × 2
    ##   GROUP LiTtEr_NuMbEr
    ##   <chr> <chr>        
    ## 1 Con7  #85          
    ## 2 Con7  #1/2/95/2    
    ## 3 Con7  #5/5/3/83/3-3
    ## # … with 46 more rows

If you want to keep the original data set (no filtering) and just rename
some variables, you can just use the `rename` function:

``` r
rename(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
```

    ## # A tibble: 49 × 8
    ##   GROUP LiTtEr_NuMbEr gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20       3       4       3
    ## 2 Con7  #1/2/95/2           27          42            19       8       0       7
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19       6       0       5
    ## # … with 46 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth, ³​pups_survive

There are many helper functions within `select` to make things easier
and faster. `starts_with()` to filter for specific variables starting
with a character, so you don’t have to list everything out:

``` r
select(litters_data, starts_with("gd"))
```

    ## # A tibble: 49 × 3
    ##   gd0_weight gd18_weight gd_of_birth
    ##        <dbl>       <dbl>       <int>
    ## 1       19.7        34.7          20
    ## 2       27          42            19
    ## 3       26          41.4          19
    ## # … with 46 more rows

The same goes for `ends_with()`

`everything()` can reorganize columns without discarding anything:

``` r
# litter_number and pups_survive go first, then everything else
select(litters_data, litter_number, pups_survive, everything())
```

    ## # A tibble: 49 × 8
    ##   litter_number pups_survive group gd0_weight gd18_wei…¹ gd_of…² pups_…³ pups_…⁴
    ##   <chr>                <int> <chr>      <dbl>      <dbl>   <int>   <int>   <int>
    ## 1 #85                      3 Con7        19.7       34.7      20       3       4
    ## 2 #1/2/95/2                7 Con7        27         42        19       8       0
    ## 3 #5/5/3/83/3-3            5 Con7        26         41.4      19       6       0
    ## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
    ## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth

`relocate` does a similar thing (and is sort of like rename in that it’s
handy but not critical):

``` r
relocate(litters_data, litter_number, pups_survive)
```

    ## # A tibble: 49 × 8
    ##   litter_number pups_survive group gd0_weight gd18_wei…¹ gd_of…² pups_…³ pups_…⁴
    ##   <chr>                <int> <chr>      <dbl>      <dbl>   <int>   <int>   <int>
    ## 1 #85                      3 Con7        19.7       34.7      20       3       4
    ## 2 #1/2/95/2                7 Con7        27         42        19       8       0
    ## 3 #5/5/3/83/3-3            5 Con7        26         41.4      19       6       0
    ## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
    ## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth

## `filter`

You can use the `filter` function to specify rows instead of columns.
Typically use logical expressions to set criteria to select observations

``` r
filter(pups_data, sex == 1)
```

    ## # A tibble: 155 × 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <int>   <int>   <int>    <int>   <int>
    ## 1 #85               1       4      13        7      11
    ## 2 #85               1       4      13        7      12
    ## 3 #1/2/95/2         1       5      13        7       9
    ## # … with 152 more rows

``` r
filter(pups_data, sex == 2, pd_walk < 11)
```

    ## # A tibble: 127 × 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <int>   <int>   <int>    <int>   <int>
    ## 1 #1/2/95/2         2       4      13        7       9
    ## 2 #1/2/95/2         2       4      13        7      10
    ## 3 #1/2/95/2         2       5      13        8      10
    ## # … with 124 more rows

You can use `drop_na()` within the function to remove missing variables

``` r
drop_na(litters_data, gd0_weight) # remove rows for which gd0_weight is missing.
```

    ## # A tibble: 34 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <int>
    ## 1 Con7  #85                 19.7        34.7          20       3       4       3
    ## 2 Con7  #1/2/95/2           27          42            19       8       0       7
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19       6       0       5
    ## # … with 31 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth, ³​pups_survive

## `mutate`

Use `mutate` to change or add new variables

``` r
mutate(litters_data,
  wt_gain = gd18_weight - gd0_weight,
  group = str_to_lower(group),
  wt_gain_kg = wt_gain * 2.2
)
```

    ## # A tibble: 49 × 10
    ##   group litter…¹ gd0_w…² gd18_…³ gd_of…⁴ pups_…⁵ pups_…⁶ pups_…⁷ wt_gain wt_ga…⁸
    ##   <chr> <chr>      <dbl>   <dbl>   <int>   <int>   <int>   <int>   <dbl>   <dbl>
    ## 1 con7  #85         19.7    34.7      20       3       4       3    15      33  
    ## 2 con7  #1/2/95…    27      42        19       8       0       7    15      33  
    ## 3 con7  #5/5/3/…    26      41.4      19       6       0       5    15.4    33.9
    ## # … with 46 more rows, and abbreviated variable names ¹​litter_number,
    ## #   ²​gd0_weight, ³​gd18_weight, ⁴​gd_of_birth, ⁵​pups_born_alive,
    ## #   ⁶​pups_dead_birth, ⁷​pups_survive, ⁸​wt_gain_kg

## `arrange`

To rearrange rows in dataset, could be by a variable.

``` r
head(arrange(litters_data, group, pups_born_alive), 10) # Sort groups, by ascending pups_born_alive
```

    ## # A tibble: 10 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_…¹ pups_…² pups_…³ pups_…⁴
    ##    <chr> <chr>                <dbl>       <dbl>    <int>   <int>   <int>   <int>
    ##  1 Con7  #85                   19.7        34.7       20       3       4       3
    ##  2 Con7  #5/4/2/95/2           28.5        44.1       19       5       1       4
    ##  3 Con7  #5/5/3/83/3-3         26          41.4       19       6       0       5
    ##  4 Con7  #4/2/95/3-3           NA          NA         20       6       0       6
    ##  5 Con7  #2/2/95/3-2           NA          NA         20       6       0       4
    ##  6 Con7  #1/2/95/2             27          42         19       8       0       7
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA         20       9       0       9
    ##  8 Con8  #2/2/95/2             NA          NA         19       5       0       4
    ##  9 Con8  #1/6/2/2/95-2         NA          NA         20       7       0       6
    ## 10 Con8  #3/6/2/2/95-3         NA          NA         20       7       0       7
    ## # … with abbreviated variable names ¹​gd_of_birth, ²​pups_born_alive,
    ## #   ³​pups_dead_birth, ⁴​pups_survive

You can also go in ascending order:

``` r
head(arrange(litters_data, desc(group), pups_born_alive), 10) # Sort descending groups, by ascending pups_born_alive
```

    ## # A tibble: 10 × 8
    ##    group litter_number gd0_weight gd18_weight gd_of_bi…¹ pups_…² pups_…³ pups_…⁴
    ##    <chr> <chr>              <dbl>       <dbl>      <int>   <int>   <int>   <int>
    ##  1 Mod8  #7/82-3-2           26.9        43.2         20       7       0       7
    ##  2 Mod8  #97                 24.5        42.8         20       8       1       8
    ##  3 Mod8  #5/93/2             NA          NA           19       8       0       8
    ##  4 Mod8  #7/110/3-2          27.5        46           19       8       1       8
    ##  5 Mod8  #82/4               33.4        52.7         20       8       0       6
    ##  6 Mod8  #2/95/2             28.5        44.5         20       9       0       9
    ##  7 Mod8  #5/93               NA          41.1         20      11       0       9
    ##  8 Mod7  #3/82/3-2           28          45.9         20       5       0       5
    ##  9 Mod7  #5/3/83/5-2         22.6        37           19       5       0       5
    ## 10 Mod7  #106                21.7        37.8         20       5       0       2
    ## # … with abbreviated variable names ¹​gd_of_birth, ²​pups_born_alive,
    ## #   ³​pups_dead_birth, ⁴​pups_survive

## `%>%`

AKA piping. Pre-piping, you could need to create multiple iterations of
your data set by step, very tedious!

``` r
litters_data_raw = read_csv("./data/FAS_litters.csv",
  col_types = "ccddiiii")
litters_data_clean_names = janitor::clean_names(litters_data_raw)
litters_data_selected_cols = select(litters_data_clean_names, -pups_survive)
litters_data_with_vars = 
  mutate(
    litters_data_selected_cols, 
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group))
litters_data_with_vars_without_missing = 
  drop_na(litters_data_with_vars, wt_gain)
litters_data_with_vars_without_missing
```

    ## # A tibble: 31 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² wt_gain
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <dbl>
    ## 1 con7  #85                 19.7        34.7          20       3       4    15  
    ## 2 con7  #1/2/95/2           27          42            19       8       0    15  
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19       6       0    15.4
    ## # … with 28 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth

Another tedious way is to nest functions within functions, but you would
have to kind of work backwards:

``` r
litters_data_clean = 
  drop_na(
    mutate(
      select(
        janitor::clean_names(
          read_csv("./data/FAS_litters.csv", col_types = "ccddiiii")
          ), 
      -pups_survive
      ),
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)
    ),
  wt_gain
  )

litters_data_clean
```

    ## # A tibble: 31 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² wt_gain
    ##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <dbl>
    ## 1 con7  #85                 19.7        34.7          20       3       4    15  
    ## 2 con7  #1/2/95/2           27          42            19       8       0    15  
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19       6       0    15.4
    ## # … with 28 more rows, and abbreviated variable names ¹​pups_born_alive,
    ## #   ²​pups_dead_birth

The pipe is an easier alternative:

``` r
# Use cmd+shift+m to call for the pipe
# Each step creates a tibble and passes it along to the next function
litters_data = 
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>% # load tibble
  janitor::clean_names() %>% # then clean
  select(-pups_survive) %>% # then select variables
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)) %>% # then edit variables
  drop_na(wt_gain) # then remove rows
```

You can use piping with a non tidyverse function:

``` r
litters_data %>%
  lm(wt_gain ~ pups_born_alive, data = .) %>%
  broom::tidy()
```

    ## # A tibble: 2 × 5
    ##   term            estimate std.error statistic  p.value
    ##   <chr>              <dbl>     <dbl>     <dbl>    <dbl>
    ## 1 (Intercept)       13.1       1.27      10.3  3.39e-11
    ## 2 pups_born_alive    0.605     0.173      3.49 1.55e- 3
