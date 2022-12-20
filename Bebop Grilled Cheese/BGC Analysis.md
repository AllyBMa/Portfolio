# Bebop Grilled Cheese
### Dashboard Case Competition
Note: This project was undertaken in a team of five. The code and analysis contained in this repo is primarily my individual contribution; unless otherwise indicated it can be assumed to be my own. The contribution of my teammates has either been left out - as I do not have their code, or I have identified it. There were also analyses and charts that did not make it to the final product. As this was created for the final project of a course and a case competition, we had some restrictions. We were required to use R and Google Sheets for data processing, and our dashboard had to be developed using Google Sheets with four to five panels

## Table of Contents

1. Case Description
2. 

## 1. Case Description
Bebop Grilled Cheese (BGC) is a food services business that operates four specialty food trucks, each with very similar menus across three product categories: main course, snack, and drink. As a specialty food truck operator, BGC is known for its gourmet grilled cheese sandwiches, special fried chicken sandwich, and veggie burgers.  All trucks also offer consumers hand cut truffle fries and a choice of different beverages.
  - Each truck targets a different consumer market given its location so Alex adjusts menu prices slightly and adapts the truck’s operating schedules to fit the target market.
  - The truck in the downtown metro area and the truck in the university district both operate year-round for six days a week.  
  - The truck in the suburban area, which is located near parks and recreational centers, operates for a six-month period – May to October – for five days a week.
  - The fourth truck operates for a six-month period – May to October – and moves to various locations around the city for special events, operating only on Fridays, Saturdays, and Sundays.
  - Each truck operates with a minimum of two BGC employees and, during the busier months, adds a third employee for specific day parts – e.g., lunch time – when traffic is typically high.
  - Chris sources all ingredients for the trucks so the average variable cost per product item for the 2022 fiscal year is the same across all four trucks.  
The co-founders provided you with a copy of the operations report for the fiscal year-ended October 31, 2022. When BGC was founded, they set a target of 70% for gross margin because the food services company margin must cover labor costs for food truck employees as well as other fixed selling, general, and admin expenses.
**Business Problem:** BGC's gross margin in the 2022 fiscal year was below the target of 70%. What led to this result and how might we adjust operations to meet 70% gross margin next fiscal year?

## 2. Data
[Data set](https://github.com/AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/bebopSalesTransactions_2022.csv)

Contains 195,728 itemized sales transactions and the following 12 variables: FY, SalesDate, month, weekDay, truckID, orderID, lineItemID, group, item, price, q, cost
  - FY = refers to the company’s fiscal year which runs from 2021-11-01 to 2022-10-31.
  - SalesDate = sales date.
  - month= month of sales date.
  - weekDay = day of the week of sales date.
  - truckID = truck ID takes four values: metroFY, specEventHY, suburbHY, uniDistFY.
  - orderID = refers to the receipt number.
  - lineItemID = refers to itemized ID of products shown in the receipt.
  - group = refers to one of the three products groups: : Drink, Main, Snack.
  - item = product name/description.
  - price = product price.
  - q = units sold.
  - cost = cost of product (cost of good sold). *note: to simplify their accounting, and understanding that ASPE does not require presentation of cost of sales, BGC has opted not to classify any expenses as cost of sales. Expenses are either classified as COGS or operating expenses, based on their nature.

## 3. Data Processing
```R
# attaching necessary packages
library(tidyverse)
# prevent scientific notation
options(scipen = 99)
# import and view data
dt0 <- read_csv("bebopSalesTransactions_2022.csv")
glimpse(dt0)
# check for missing data
dt0 %>% is.na() %>% colSums()
```
Perfect, no missing data.
However, there are some variables that will come in handy. Revenue is something that management wanted to examine, as well as a main input along with cogs for gross profit and gross profit margin.
```R
# new data set with revenue, cost of goods sold, 
# gross profit, and gross profit margin
dt1 <- dt0 %>% 
  mutate(
    revenue = price*q,
    cogs = cost*q,
    gp = revenue - cogs,
    gpm = gp/revenue
  )
glimpse(dt1)
```
### Panel 1
Our first panel was to provide an overview of sales revenue, cogs, gross profit, and pross margin sales revenue and gross margin by month for BGC (total company) and for each of the trucks, so we included three elements.

My teammates provided the overview in dollars for BGC, and the analysis by month by truck/BGC
