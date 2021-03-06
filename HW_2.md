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

# Problem 1

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

total_2018 = sum(pull(data_2018, Total))
trash_2019 = filter(trash_data, year == "2019")
sports_balls_median_2019 = median(pull(trash_2019, sports_balls))
```

In the trash\_data, there are 344 rows and 14 columns. There are
“dumpster”,“month”,“year”, “date”, “weight\_tons”,
“volume\_cubic\_yards”, “plastic\_bottles”, “polystyrene”,
“cigarette\_butts”, “glass\_bottles”, “grocery\_bags”, “chip\_bags”,
“sports\_balls”, “homes\_powered” observations. In data\_2018, there are
6 rows and 3 columns. There are “Month” “Total” “Year” observations. In
data\_2019, there are 12 rows and 14 columns. There are “Month” “Total”
“Year” observations. the total precipitation in 2018 is 70.33. the
median number of sports balls in a dumpster in 2019 is 9.

# Problem 2

``` r
# clean the data in pols-month.csv. 
pols_month = read_csv("pols-month.csv") %>% 
  janitor::clean_names() %>%
  separate(mon, into = c("year", "month","day")) %>% 
  mutate(month = month.name[as.numeric(month)]) %>% 
  mutate(president = ifelse(prez_gop==1, "gop", "dem")) %>% 
  select(-prez_dem) %>% 
  select(-prez_gop) %>% 
  select(-day)
```

    ## Rows: 822 Columns: 9

    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl  (8): prez_gop, gov_gop, sen_gop, rep_gop, prez_dem, gov_dem, sen_dem, r...
    ## date (1): mon

    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# clean the data in snp.csv
snp <- read_csv("snp.csv")%>% 
  janitor::clean_names() %>% 
  separate(date, into = c("month","day","year")) %>% 
    arrange(year, month) %>% 
  mutate(month = month.name[as.numeric(month)]) %>% 
  relocate(year, month) %>% 
  mutate(year = as.numeric(year)) %>% 
  mutate(year = ifelse(
    year >30, year + 1900 , year + 2000
  )) %>% 
  mutate(year = as.character(year))
```

    ## Rows: 787 Columns: 2

    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): date
    ## dbl (1): close

    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# year_format = pull(snp, year)
# year_format = as.numeric(year_format)
# year_format = ifelse(year_format>30, year_format+1900, year_format+2000)
# year_format = as.character(year_format)
# snp = select(snp, -year )%>% 
#   mutate(year = year_format) %>% 
#   relocate(year, month)

# tidy the unemployment data so that it can be merged with the previous datasets.                
unemployment_data = read_csv("unemployment.csv") %>% 
  janitor::clean_names() %>%
  pivot_longer(jan:dec,
                names_to = "month",
                values_to = "unemployment") %>% 
  mutate(month, month = recode(month, "jan"="January", "feb" = "February",  "mar" = "March", "apr" = "April",  "may" = "May", "jun" = "June" , "jul" = "July", "aug"= "August",  "sep" = "September",  "oct"="October" , "nov" = "November", "dec" = "December")) %>% 
  mutate(year = as.character(year)) %>% 
  relocate(year, month)
```

    ## Rows: 68 Columns: 13

    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (13): Year, Jan, Feb, Mar, Apr, May, Jun, Jul, Aug, Sep, Oct, Nov, Dec

    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# tidy the unemployment data so that it can be merged with the previous datasets. 
snp_pols_unemployment_data = pols_month %>% 
  left_join(snp) %>% 
  left_join(unemployment_data)
```

    ## Joining, by = c("year", "month")

    ## Joining, by = c("year", "month")

In the first dataset, pols\_month, there are 822 rows and 9 columns. it
contains year, month, date, and relative number of senators,
representatives, governors in two parties.

In the second dataset, snp, there are 787 rows and 4 columns. it
contains year, month, date and close value.

In the third dataset, unemployment, there are 816 rows and 3 columns. it
contains year, month and unemployment value.

In the resulting dataset, there are 822 rows and 12 columns. the range
of years is from 1947 to 2015. the key variables are “year” , “month” ,
“gov\_gop”, “sen\_gop”, “rep\_gop”, “gov\_dem”, “sen\_dem”, “rep\_dem”,
“president”, “day”, “close”, “unemployment”.

# Problem 3

``` r
# Clean the data
baby_data = read_csv("Popular_Baby_Names.csv") %>% 
  janitor::clean_names() %>% 
  distinct() %>% 
  arrange(year_of_birth) %>% 
  mutate(ethnicity = str_to_lower(ethnicity)) %>% 
  mutate(childs_first_name = str_to_lower(childs_first_name)) %>% 
  mutate(gender = str_to_lower(gender)) %>% 
  mutate(ethnicity, ethnicity = recode(ethnicity, "black non hisp" = "black non hispanic", "white non hisp" = "white non hispanic", "asian and paci" = "asian and pacific islander"))
```

    ## Rows: 19418 Columns: 6

    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (3): Gender, Ethnicity, Child's First Name
    ## dbl (3): Year of Birth, Count, Rank

    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# produce a well-structured, reader-friendly table showing the rank in popularity of the name “Olivia” as a female baby name over time
olivia_data = filter(baby_data, childs_first_name == "olivia") %>% 
  select(-gender, -count, -childs_first_name) %>% 
  pivot_wider(
    names_from = "year_of_birth",
    values_from = "rank"
  )

# Produce a well-structured, reader-friendly table showing the most popular name among male children over time  
male_most_data = filter(baby_data, gender == "male", rank == "1")  %>% 
  select(-gender, -rank, -count) %>% 
  pivot_wider(
    names_from = "year_of_birth",
    values_from = "childs_first_name"
  )


# Produce a scatter plot showing the number of children with a name (y axis) against the rank in popularity of that name (x axis).
male_non_hispanic_2016 = filter(baby_data, year_of_birth == "2016", gender =="male", ethnicity == "white non hispanic" )
ggplot(male_non_hispanic_2016, aes(x = pull(male_non_hispanic_2016, rank), y = pull(male_non_hispanic_2016, count))) + geom_point()
```

![](HW_2_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->
