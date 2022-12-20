
# Bebop Grilled Cheese

### Dashboard Case Competition

Note: This project was undertaken in a team of five. The code and
analysis contained in this repo is primarily my individual contribution;
unless otherwise indicated it can be assumed to be my own. The
contribution of my teammates has either been left out - as I do not have
their code, or I have identified it. There were also analyses and charts
that did not make it to the final product. As this was created for the
final project of a course and a case competition, we had some
restrictions. We were required to use R and Google Sheets for data
processing, and our dashboard had to be developed using Google Sheets
with four to five panels

## Table of Contents

1.  Case Description
2.  

## 1. Case Description

Bebop Grilled Cheese (BGC) is a food services business that operates
four specialty food trucks, each with very similar menus across three
product categories: main course, snack, and drink. As a specialty food
truck operator, BGC is known for its gourmet grilled cheese sandwiches,
special fried chicken sandwich, and veggie burgers. All trucks also
offer consumers hand cut truffle fries and a choice of different
beverages.

- Each truck targets a different consumer market given its location so
  Alex adjusts menu prices slightly and adapts the truck’s operating
  schedules to fit the target market.
- The truck in the downtown metro area and the truck in the university
  district both operate year-round for six days a week.  
- The truck in the suburban area, which is located near parks and
  recreational centers, operates for a six-month period – May to October
  – for five days a week.
- The fourth truck operates for a six-month period – May to October –
  and moves to various locations around the city for special events,
  operating only on Fridays, Saturdays, and Sundays.
- Each truck operates with a minimum of two BGC employees and, during
  the busier months, adds a third employee for specific day parts –
  e.g., lunch time – when traffic is typically high. The co-founders
  provided you with a copy of the operations report for the fiscal
  year-ended October 31, 2022. When BGC was founded, they set a target
  of 70% for gross margin because the food services company margin must
  cover labor costs for food truck employees as well as other fixed
  selling, general, and admin expenses. **Business Problem:** BGC’s
  gross margin in the 2022 fiscal year was below the target of 70%. What
  led to this result and how might we adjust operations to meet 70%
  gross margin next fiscal year?

## 2. Data

[Data
set](https://github.com/AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/bebopSalesTransactions_2022.csv)

Contains 195,728 itemized sales transactions and the following 12
variables: FY, SalesDate, month, weekDay, truckID, orderID, lineItemID,
group, item, price, q, cost

- FY = refers to the company’s fiscal year which runs from 2021-11-01 to
  2022-10-31.
- SalesDate = sales date.
- month= month of sales date.
- weekDay = day of the week of sales date.
- truckID = truck ID takes four values: metroFY, specEventHY, suburbHY,
  uniDistFY.
- orderID = refers to the receipt number.
- lineItemID = refers to itemized ID of products shown in the receipt.
- group = refers to one of the three products groups: : Drink, Main,
  Snack.
- item = product name/description.
- price = product price.
- q = units sold.
- cost = cost of product (cost of good sold). \*note: to simplify their
  accounting, and understanding that ASPE does not require presentation
  of cost of sales, BGC has opted not to classify any expenses as cost
  of sales. Expenses are either classified as COGS or operating
  expenses, based on their nature.

## 3. Data Processing

You can include R code in the document as follows:

``` r
# attaching necessary packages
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.4.0      ✔ purrr   0.3.5 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.1      ✔ stringr 1.4.1 
    ## ✔ readr   2.1.3      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
# prevent scientific notation
options(scipen = 99)
# import and view data
dt0 <- read_csv("bebopSalesTransactions_2022.csv")
```

    ## Rows: 195728 Columns: 12
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (6): weekDay, truckID, orderID, lineItemID, group, item
    ## dbl  (5): FY, month, price, q, cost
    ## date (1): SalesDate
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
glimpse(dt0)
```

    ## Rows: 195,728
    ## Columns: 12
    ## $ FY         <dbl> 2022, 2022, 2022, 2022, 2022, 2022, 2022, 2022, 2022, 2022,…
    ## $ SalesDate  <date> 2021-11-01, 2021-11-01, 2021-11-01, 2021-11-01, 2021-11-01…
    ## $ month      <dbl> 11, 11, 11, 11, 11, 11, 11, 11, 11, 11, 11, 11, 11, 11, 11,…
    ## $ weekDay    <chr> "Monday", "Monday", "Monday", "Monday", "Monday", "Monday",…
    ## $ truckID    <chr> "metroFY", "metroFY", "metroFY", "metroFY", "metroFY", "met…
    ## $ orderID    <chr> "metroFY202211_0000", "metroFY202211_0000", "metroFY202211_…
    ## $ lineItemID <chr> "00001", "00002", "00003", "00005", "00006", "00008", "0000…
    ## $ group      <chr> "Snack", "Main", "Drink", "Main", "Drink", "Main", "Drink",…
    ## $ item       <chr> "Hand Cut Truffle Fries", "Veggie Burger", "Coffee", "Grill…
    ## $ price      <dbl> 4.81, 7.94, 1.49, 8.50, 1.49, 7.94, 2.10, 6.49, 2.10, 4.81,…
    ## $ q          <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
    ## $ cost       <dbl> 1.44, 3.10, 0.25, 2.55, 0.25, 3.10, 0.49, 1.95, 0.49, 1.44,…

``` r
# check for missing data
dt0 %>% is.na() %>% colSums()
```

    ##         FY  SalesDate      month    weekDay    truckID    orderID lineItemID 
    ##          0          0          0          0          0          0          0 
    ##      group       item      price          q       cost 
    ##          0          0          0          0          0

## Including Plots

You can also embed plots, for example:

![](test_files/figure-gfm/pressure-1.png)<!-- -->

Note that the `echo = FALSE` parameter was added to the code chunk to
prevent printing of the R code that generated the plot.
