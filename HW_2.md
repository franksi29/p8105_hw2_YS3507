hw2
================
Yu Si
10/9/2021

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.4     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   2.0.1     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(readxl)
library(haven)
```

\#\#Problem 1

``` r
# Read and clean the Mr. Trash Wheel sheet
trash_data = read_excel("./Trash-Wheel-Collection-Totals-7-2020-2.xlsx", col_names = TRUE, range = "A2:N534")%>% 
  janitor::clean_names() %>% 
  drop_na(dumpster) %>% 
  mutate(sports_balls = round(sports_balls))

#Read and clean precipitation data for 2018 and 2019. 
data_2018 = read_excel("./Trash-Wheel-Collection-Totals-7-2020-2.xlsx", sheet = 7, range = "A2:B14") %>% 
  drop_na() %>% 
  mutate(Year = "2018")
data_2019 = read_excel("./Trash-Wheel-Collection-Totals-7-2020-2.xlsx", sheet = 6, range = "A2:B14") %>% 
  drop_na() %>% 
  mutate(Year = "2019")

#Combine precipitation datasets and convert month to a character variable 
data_2018_2019 = bind_rows(data_2018, data_2019) %>% 
  mutate(Month = month.name[Month])
```
