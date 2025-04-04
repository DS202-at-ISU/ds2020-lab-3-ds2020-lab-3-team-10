
<!-- README.md is generated from README.Rmd. Please edit the README.Rmd file -->

# Lab report \#3 - instructions

Follow the instructions posted at
<https://ds202-at-isu.github.io/labs.html> for the lab assignment. The
work is meant to be finished during the lab time, but you have time
until Monday evening to polish things.

Include your answers in this document (Rmd file). Make sure that it
knits properly (into the md file). Upload both the Rmd and the md file
to your repository.

All submissions to the github repo will be automatically uploaded for
grading once the due date is passed. Submit a link to your repository on
Canvas (only one submission per team) to signal to the instructors that
you are done with your submission.

# Lab 3: Avenger’s Peril

## As a team

Extract from the data below two data sets in long form `deaths` and
`returns`

``` r
av <- read.csv("https://raw.githubusercontent.com/fivethirtyeight/data/master/avengers/avengers.csv", stringsAsFactors = FALSE)
head(av)
```

    ##                                                       URL
    ## 1           http://marvel.wikia.com/Henry_Pym_(Earth-616)
    ## 2      http://marvel.wikia.com/Janet_van_Dyne_(Earth-616)
    ## 3       http://marvel.wikia.com/Anthony_Stark_(Earth-616)
    ## 4 http://marvel.wikia.com/Robert_Bruce_Banner_(Earth-616)
    ## 5        http://marvel.wikia.com/Thor_Odinson_(Earth-616)
    ## 6       http://marvel.wikia.com/Richard_Jones_(Earth-616)
    ##                    Name.Alias Appearances Current. Gender Probationary.Introl
    ## 1   Henry Jonathan "Hank" Pym        1269      YES   MALE                    
    ## 2              Janet van Dyne        1165      YES FEMALE                    
    ## 3 Anthony Edward "Tony" Stark        3068      YES   MALE                    
    ## 4         Robert Bruce Banner        2089      YES   MALE                    
    ## 5                Thor Odinson        2402      YES   MALE                    
    ## 6      Richard Milhouse Jones         612      YES   MALE                    
    ##   Full.Reserve.Avengers.Intro Year Years.since.joining Honorary Death1 Return1
    ## 1                      Sep-63 1963                  52     Full    YES      NO
    ## 2                      Sep-63 1963                  52     Full    YES     YES
    ## 3                      Sep-63 1963                  52     Full    YES     YES
    ## 4                      Sep-63 1963                  52     Full    YES     YES
    ## 5                      Sep-63 1963                  52     Full    YES     YES
    ## 6                      Sep-63 1963                  52 Honorary     NO        
    ##   Death2 Return2 Death3 Return3 Death4 Return4 Death5 Return5
    ## 1                                                            
    ## 2                                                            
    ## 3                                                            
    ## 4                                                            
    ## 5    YES      NO                                             
    ## 6                                                            
    ##                                                                                                                                                                              Notes
    ## 1                                                                                                                Merged with Ultron in Rage of Ultron Vol. 1. A funeral was held. 
    ## 2                                                                                                  Dies in Secret Invasion V1:I8. Actually was sent tto Microverse later recovered
    ## 3 Death: "Later while under the influence of Immortus Stark committed a number of horrible acts and was killed.'  This set up young Tony. Franklin Richards later brought him back
    ## 4                                                                               Dies in Ghosts of the Future arc. However "he had actually used a hidden Pantheon base to survive"
    ## 5                                                      Dies in Fear Itself brought back because that's kind of the whole point. Second death in Time Runs Out has not yet returned
    ## 6                                                                                                                                                                             <NA>

Get the data into a format where the five columns for Death\[1-5\] are
replaced by two columns: Time, and Death. Time should be a number
between 1 and 5 (look into the function `parse_number`); Death is a
categorical variables with values “yes”, “no” and ““. Call the resulting
data set `deaths`.

``` r
library(dplyr)
```

    ## Warning: package 'dplyr' was built under R version 4.4.3

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ forcats   1.0.0     ✔ readr     2.1.5
    ## ✔ ggplot2   3.5.1     ✔ stringr   1.5.1
    ## ✔ lubridate 1.9.3     ✔ tibble    3.2.1
    ## ✔ purrr     1.0.2     ✔ tidyr     1.3.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
deaths <- av %>% 
  pivot_longer(
    starts_with("Death"),
    names_to = "Time",
    values_to = "Died"
  ) %>% 
  select(
    URL, Name.Alias, Time, Died
  ) %>%
  mutate(
    Time = parse_number(Time)
  )
head(deaths)
```

    ## # A tibble: 6 × 4
    ##   URL                                                Name.Alias       Time Died 
    ##   <chr>                                              <chr>           <dbl> <chr>
    ## 1 http://marvel.wikia.com/Henry_Pym_(Earth-616)      "Henry Jonatha…     1 "YES"
    ## 2 http://marvel.wikia.com/Henry_Pym_(Earth-616)      "Henry Jonatha…     2 ""   
    ## 3 http://marvel.wikia.com/Henry_Pym_(Earth-616)      "Henry Jonatha…     3 ""   
    ## 4 http://marvel.wikia.com/Henry_Pym_(Earth-616)      "Henry Jonatha…     4 ""   
    ## 5 http://marvel.wikia.com/Henry_Pym_(Earth-616)      "Henry Jonatha…     5 ""   
    ## 6 http://marvel.wikia.com/Janet_van_Dyne_(Earth-616) "Janet van Dyn…     1 "YES"

``` r
deaths %>% count(Died)
```

    ## # A tibble: 3 × 2
    ##   Died      n
    ##   <chr> <int>
    ## 1 ""      671
    ## 2 "NO"    105
    ## 3 "YES"    89

Similarly, deal with the returns of characters.

``` r
library(dplyr)
library(tidyverse)

returns <- av %>% 
  pivot_longer(
    starts_with("Return"),
    names_to = "TimesReturn",
    values_to = "Return"
  ) %>% 
  select(
    URL, Name.Alias, TimesReturn, Return
  ) %>%
  mutate(
    TimesReturn = parse_number(TimesReturn)
  )
head(returns)
```

    ## # A tibble: 6 × 4
    ##   URL                                              Name.Alias TimesReturn Return
    ##   <chr>                                            <chr>            <dbl> <chr> 
    ## 1 http://marvel.wikia.com/Henry_Pym_(Earth-616)    "Henry Jo…           1 "NO"  
    ## 2 http://marvel.wikia.com/Henry_Pym_(Earth-616)    "Henry Jo…           2 ""    
    ## 3 http://marvel.wikia.com/Henry_Pym_(Earth-616)    "Henry Jo…           3 ""    
    ## 4 http://marvel.wikia.com/Henry_Pym_(Earth-616)    "Henry Jo…           4 ""    
    ## 5 http://marvel.wikia.com/Henry_Pym_(Earth-616)    "Henry Jo…           5 ""    
    ## 6 http://marvel.wikia.com/Janet_van_Dyne_(Earth-6… "Janet va…           1 "YES"

Based on these datasets calculate the average number of deaths an
Avenger suffers.

``` r
totalPeople = length(unique(deaths$Name.Alias))

totalDeaths = length(which(deaths$Died == "YES"))
max(deaths$Time)
```

    ## [1] 5

``` r
totalPeople
```

    ## [1] 163

``` r
totalDeaths
```

    ## [1] 89

``` r
totalDeaths / totalPeople
```

    ## [1] 0.5460123

## Individually

For each team member, copy this part of the report.

Each team member picks one of the statements in the FiveThirtyEight
[analysis](https://fivethirtyeight.com/features/avengers-death-comics-age-of-ultron/)
and fact checks it based on the data. Use dplyr functionality whenever
possible.

### FiveThirtyEight Statement

Isaac’s Statement: \> “But you can only tempt death so many times.
There’s a 2-in-3 chance that a member of the Avengers returned from
their first stint in the afterlife”

### Include the code

``` r
death1 <- filter(deaths, Died == "1")
```

### Include your answer

Include at least one sentence discussing the result of your
fact-checking endeavor.

Cameron Kraklio Iron Man, Hulk, Captain America, Thor, Hawkeye, Black
Widow, Scarlet Witch, Quicksilver and The Vision — every single one of
them has died at least once in the course of their time Avenging in the
comics. In fact, Hawkeye died twice

Upload your changes to the repository. Discuss and refine answers as a
team.

## Eitan’s Work

### FiveThirtyEight Statement

The statement I chose to fact check is “The MVP of the Earth-616 Marvel
Universe Avengers has to be Jocasta — an android based on Janet van Dyne
and built by Ultron — who has been destroyed five times and then
recovered five times.”

### Include the code

Code for fact-checking statement.

``` r
# Check Jocasta's deaths
jocasta_deaths <- deaths %>% 
  filter(Name.Alias == "Jocasta", Died == "YES") %>% 
  count()

# Check Jocasta's returns
jocasta_returns <- returns %>% 
  filter(Name.Alias == "Jocasta", Return == "YES") %>% 
  count()

if (jocasta_returns == jocasta_deaths) {
  print("Statement is true.")
} else {
   print("Statement is false.")
}
```

    ## [1] "Statement is true."

### Include your answer

My conclusion is that the statement is true and has been fact-checked
since the number of times Jocasta has been destroyed and returned is
equivalent.

### Naman's work

### FiveThirtyEight Statement

Given the Avengers’ 53 years in operation and overall mortality rate, fans of the comics can expect one current or former member to die every seven months or so, with a permanent death occurring once every 20 months.

library(dplyr)


avengers <- read.csv("avengers.csv")


years_in_operation <- 53


total_deaths <- avengers %>%
  filter(!is.na(year_died)) %>%
  summarise(total = n()) %>%
  pull(total)


death_rate <- total_deaths / (years_in_operation * 12)


permanent_deaths <- avengers %>%
  filter(permanent_death == 1) %>%
  summarise(total = n()) %>%
  pull(total)


permanent_death_rate <- permanent_deaths / (years_in_operation * 12)


death_interval <- 1 / death_rate
permanent_death_interval <- 1 / permanent_death_rate

### Include your answer

The statement is true
