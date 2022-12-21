
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
with four to five panels.

## Table of Contents

1.  [Case Description](AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/BGCReport.md#1-case-description)
2.  [Data Description](AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/BGCReport.md#2-data-description)
3.  [Data Processing and Analysis](AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/BGCReport.md#3-data-processing-and-analysis)
4.  [Data Visualization](AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/BGCReport.md#4-data-visualization)
5.  [Key Points](AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/BGCReport.md#5-key-points)
6.  [Recommendation](AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/BGCReport.md#6-recommendation)

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
  e.g., lunch time – when traffic is typically high.
- Chris sources all ingredients for the trucks so the average variable
  cost per product item for the 2022 fiscal year is the same across all
  four trucks. The co-founders provided you with a copy of the
  operations report for the fiscal year-ended October 31, 2022. When BGC
  was founded, they set a target of 70% for gross margin because the
  food services company margin must cover labor costs for food truck
  employees as well as other fixed selling, general, and admin expenses.
  **Business Problem:** BGC’s gross margin in the 2022 fiscal year was
  below the target of 70%. What led to this result and how might we
  adjust operations to meet 70% gross margin next fiscal year?

## 2. Data Description

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

## 3. Data Processing and Analysis

``` r
# attaching necessary packages
library(tidyverse)
# prevent scientific notation
options(scipen = 99)
# import and view data
dt0 <- read_csv("bebopSalesTransactions_2022.csv")
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

Perfect, no missing data. However, there are some variables that come in
handy. Revenue is something that management wanted to examine, as well
as a main input along with COGS for gross profit and gross profit
margin.

``` r
# new data set with revenue, cost of goods sold, 
# gross profit, and gross profit margin
dt1 <- dt0 %>% 
  mutate(
    revenue = price*q,
    cogs = cost*q,
    gp = revenue - cogs,
    gpm = gp/revenue
  )
glimpse(dt1) #view new data set
```

    ## Rows: 195,728
    ## Columns: 16
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
    ## $ revenue    <dbl> 4.81, 7.94, 1.49, 8.50, 1.49, 7.94, 2.10, 6.49, 2.10, 4.81,…
    ## $ cogs       <dbl> 1.44, 3.10, 0.25, 2.55, 0.25, 3.10, 0.49, 1.95, 0.49, 1.44,…
    ## $ gp         <dbl> 3.37, 4.84, 1.24, 5.95, 1.24, 4.84, 1.61, 4.54, 1.61, 3.37,…
    ## $ gpm        <dbl> 0.7006237, 0.6095718, 0.8322148, 0.7000000, 0.8322148, 0.60…

Now that we have our data with a more complete set of variables, let’s
pull out the things needed for each panel.

### Panel 1

Our first panel was to provide and overview of sales revenue, COGS,
gross profit, and gross margin; and sales revenue and gross margin by
month by truck/BGC, so we included three elements.

My teammates provided the overview in dollars for BGC, and the analysis
by month by truck/BGC. I worked on revenue distribution - how much went
to covering COGS and how much was profit.

``` r
panelone <- dt1 %>% 
  select(
    month, truckID, revenue, cogs, gp, gpm
  ) %>% 
  group_by(month, truckID) %>% 
  summarize(SUMrev = sum(revenue),
            SUMcogs = sum(cogs),
            SUMgp = sum(gp),
            SUMgpm = sum(gpm)
            )
panelone #revenue, cogs, gp, and gpm of each truck by month
```

    ## # A tibble: 36 × 6
    ## # Groups:   month [12]
    ##    month truckID     SUMrev SUMcogs  SUMgp SUMgpm
    ##    <dbl> <chr>        <dbl>   <dbl>  <dbl>  <dbl>
    ##  1     1 metroFY     18793.   5750. 13043.  2803.
    ##  2     1 uniDistFY   23136.   6784. 16353.  2909.
    ##  3     2 metroFY     18268.   5587. 12682.  2729.
    ##  4     2 uniDistFY   22710.   6697. 16013.  2842.
    ##  5     3 metroFY     23492.   7188. 16304.  3496.
    ##  6     3 uniDistFY   28965.   8535. 20430.  3632.
    ##  7     4 metroFY     28571.   8770. 19801.  4235.
    ##  8     4 uniDistFY   35016.  10337. 24679.  4371.
    ##  9     5 metroFY     34385.  10538. 23847.  5105.
    ## 10     5 specEventHY 18459.   5741. 12718.  2529.
    ## # … with 26 more rows

I then summarized this data in two pivot tables. Due to overlap with my
teammate’s chart, I ended up not using the grouping by month.

From these pivot tables we can see the dollar value of COGS and gross
profit for each truck and BGC as a whole. For example, truck metroFY
made a gross profit of \$251144.97, and spent \$110859.98 on COGS. 

<img src="https://github.com/AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/Media/Panel1_COGSpivot.jpg" height="100" /> <img src="https://github.com/AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/Media/Panel1_GPpivot.jpg" height="100" />

### Panel 2

Our second panel examined sales revenue for the year by truck, segmented
by product item and grouped into the three product categories: main,
snack, and drink.

``` r
paneltwo <- dt1 %>% 
  select(truckID, group, item, revenue)
paneltwo # revenue made by each item at each truck
```

    ## # A tibble: 195,728 × 4
    ##    truckID group item                                               revenue
    ##    <chr>   <chr> <chr>                                                <dbl>
    ##  1 metroFY Snack Hand Cut Truffle Fries                                4.81
    ##  2 metroFY Main  Veggie Burger                                         7.94
    ##  3 metroFY Drink Coffee                                                1.49
    ##  4 metroFY Main  Grilled Cheese - Honey Bacon on Triple Cheese Melt    8.5 
    ##  5 metroFY Drink Coffee                                                1.49
    ##  6 metroFY Main  Veggie Burger                                         7.94
    ##  7 metroFY Drink Polar Soda                                            2.1 
    ##  8 metroFY Main  Grilled Cheese - Triple Cheese Melt                   6.49
    ##  9 metroFY Drink Polar Soda                                            2.1 
    ## 10 metroFY Snack Hand Cut Truffle Fries                                4.81
    ## # … with 195,718 more rows

After pulling out the relevant data, I continued data processing with
pivot tables. We can see how much revenue each product earns at each
truck/BGC. For BGC as a whole, Hand-Cut Truffle Fries earn \$160745.39
of revenue.
<img src="https://github.com/AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/Media/Panel2_ITEMpivot.jpg" height="100" />

In this table we can see what percentage of revenue each product group
makes up for each truck/BGC. While Snack makes up \~15% for the other
trucks, it makes up 31.16% for truck specEventHY. Main is the dominant
product group for all trucks/BGC.
<img src="https://github.com/AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/Media/Panel2_GROUPpivot.jpg" height="100" />

### Panel 3

Panel three compared sales quantities and average sales prices by
product item for BGC (total company) and for each of the trucks.

``` r
dtprice <- dt1 %>%
  group_by(item)%>%
  summarize(itemavgprice=mean(price))
dtprice # average price of item across food trucks
```

    ## # A tibble: 17 × 2
    ##    item                                                itemavgprice
    ##    <chr>                                                      <dbl>
    ##  1 Coffee                                                      1.49
    ##  2 Double Fried Chicken Sandwich                               8.95
    ##  3 Grilled Cheese - Bacon Guacamole Triple Cheese Melt         8.5 
    ##  4 Grilled Cheese - Buffalo Tofu Melt                          8.75
    ##  5 Grilled Cheese - Classic Melt                               6.49
    ##  6 Grilled Cheese - Goat Cheese and Jam                        7.25
    ##  7 Grilled Cheese - Honey Bacon on Triple Cheese Melt          8.5 
    ##  8 Grilled Cheese - Plum Tomato Melt                           6.89
    ##  9 Grilled Cheese - Triple Cheese Melt                         6.49
    ## 10 Hand Cut Truffle Fries                                      4.81
    ## 11 Polar Soda                                                  2.1 
    ## 12 RedBull                                                     5.5 
    ## 13 Soft-Serve Ice Cream                                        4   
    ## 14 Spindrift                                                   2.54
    ## 15 Strawberry Banana Juice                                     3.5 
    ## 16 Vegan Veggie Burger                                         7.94
    ## 17 Veggie Burger                                               7.94

``` r
dtq <-dt1 %>%
  group_by(group, item, truckID)%>%
  summarize(SUMq=sum(q))
dtq # number of each item sold at each truck
```

    ## # A tibble: 56 × 4
    ## # Groups:   group, item [17]
    ##    group item       truckID      SUMq
    ##    <chr> <chr>      <chr>       <dbl>
    ##  1 Drink Coffee     metroFY     14293
    ##  2 Drink Polar Soda metroFY     10209
    ##  3 Drink Polar Soda specEventHY  3691
    ##  4 Drink Polar Soda suburbHY     3525
    ##  5 Drink Polar Soda uniDistFY    4968
    ##  6 Drink RedBull    uniDistFY   11023
    ##  7 Drink Spindrift  metroFY      6533
    ##  8 Drink Spindrift  specEventHY  2679
    ##  9 Drink Spindrift  suburbHY     5245
    ## 10 Drink Spindrift  uniDistFY    7610
    ## # … with 46 more rows

``` r
panelthree <- full_join(dtprice, dtq) #combine price and q
```

Note: There was an error in the case, it said prices of each item varied
across the food trucks, but the prices in the data did not. As such
price and avg price are the same, but we chose to follow the case and
talk about avg price.

Once again, the data was processed in pivot tables - one for average
price and one for quantity. Comparing the two, we see that as average
price rises, quantity decreases.
<img src="https://github.com/AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/Media/Panel3_AVGPRICEpivot.jpg" height="100" />
<img src="https://github.com/AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/Media/Panel3_SALESQpivot.jpg" height="100" />

### Panel 4

Data processed by teammate. Compares sales quantity of a specific truck
to the average sales quantity of all trucks for each day of the week.

### Panel 5

The aim of panel five was to identify well performing product items and
groups and areas of improvement.

``` r
dtproduct <- dt1 %>%
  group_by(item)%>%
  select(truckID, item, group, gpm)%>%
  distinct()
dtproduct # profit margin of each item
```

    ## # A tibble: 56 × 4
    ## # Groups:   item [17]
    ##    truckID item                                                group   gpm
    ##    <chr>   <chr>                                               <chr> <dbl>
    ##  1 metroFY Hand Cut Truffle Fries                              Snack 0.701
    ##  2 metroFY Veggie Burger                                       Main  0.610
    ##  3 metroFY Coffee                                              Drink 0.832
    ##  4 metroFY Grilled Cheese - Honey Bacon on Triple Cheese Melt  Main  0.7  
    ##  5 metroFY Polar Soda                                          Drink 0.767
    ##  6 metroFY Grilled Cheese - Triple Cheese Melt                 Main  0.700
    ##  7 metroFY Grilled Cheese - Buffalo Tofu Melt                  Main  0.699
    ##  8 metroFY Spindrift                                           Drink 0.650
    ##  9 metroFY Grilled Cheese - Bacon Guacamole Triple Cheese Melt Main  0.7  
    ## 10 metroFY Double Fried Chicken Sandwich                       Main  0.610
    ## # … with 46 more rows

``` r
dtgroup <- dt1 %>% 
  group_by(group, truckID) %>% 
  select(truckID, group, gpm) %>% 
  summarize(groupPM = mean(gpm)) %>% 
  distinct()
dtgroup # profit margin of each group
```

    ## # A tibble: 12 × 3
    ## # Groups:   group [3]
    ##    group truckID     groupPM
    ##    <chr> <chr>         <dbl>
    ##  1 Drink metroFY       0.772
    ##  2 Drink specEventHY   0.717
    ##  3 Drink suburbHY      0.656
    ##  4 Drink uniDistFY     0.749
    ##  5 Main  metroFY       0.678
    ##  6 Main  specEventHY   0.677
    ##  7 Main  suburbHY      0.678
    ##  8 Main  uniDistFY     0.677
    ##  9 Snack metroFY       0.701
    ## 10 Snack specEventHY   0.700
    ## 11 Snack suburbHY      0.701
    ## 12 Snack uniDistFY     0.701

One last time, I placed the data in pivot tables.

It can be seen that Coffee is the item with the highest profit margin of
83%; and Strawberry Banana Juice, Double Fried Chicken Sandwich, Vegan
Veggie Burger, and Veggie Burger have the lowest profit margin of 61%.
<img src="https://github.com/AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/Media/Panel5_ITEMpivot.jpg" height="100" />

From this pivot table, we see that the profit margin of product group
Main is consistently under target.
<img src="https://github.com/AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/Media/Panel5_GROUPpivot.jpg" height="100" />

## 4. Data Visualization

Our dashboard prepared using Google Sheets and was built to follow [Tufte’s Principles for Visualization](https://docs.google.com/presentation/d/11FFz8X6pH8nGrmIDJbvRfTDCdPK60Ii9RAuQmiFvBm4/edit#slide=id.g19a0f2ce771_0_272). Interactive functionality on Google Sheets is limited, but we used VLOOKUP and data validation to update the entire dashboard with a change to one cell, outlined in purple beside Truck Selection. Each chart can also be selected and hovered over for more details. Conditional formatting and data point formatting were used to keep a consistent colour and labelling scheme.

<img src="https://github.com/AllyBMa/Portfolio/blob/main/Bebop%20Grilled%20Cheese/Media/Dashboard%20Demo.gif" height="500" />

### Panel 1

Two other elements of visualization created by teammates. Used
`=VLOOKUP(IF(Dashboard!$I$2='Data Validation'!$A$6,"Grand Total",Dashboard!$I$2),$I$1:$J$6,2,false)`
and
`=VLOOKUP(IF(Dashboard!$I$2='Data Validation'!$A$6,"Grand Total",Dashboard!$I$2),$I$8:$J$13,2,false)`
to find respectively the COGS and gross profit values of the selected
truck. Then created a pie chart from these two values to present the
percentage of gross profit (gross margin) and cogs that make up revenue.

A gauge would be a better visualization of gross margin, but gauge
charts in Google Sheets are extremely limited and frankly don’t look too
great.

### Panel 2

**Element 1:** Used `=vlookup(If(Dashboard!$I$2="BGC (all)","Grand Total",Dashboard!$I$2),$A$4:$V$8,B11,false)`
to find the revenue earned by each item of the truck selected. Created a
column chart from these values. 

**Element 2:** Used `=vlookup(If(Dashboard!$I$2="BGC (all)","Grand Total",Dashboard!$I$2),$A$23:$E$29,2,false)`
to find the percentage of revenue that each product group made up for
the selected truck. Used these values to create a donut chart. 

**Element 3:** Used `=FILTER('Panel 2 Pivot'!$12:$12, 'Panel 2 Pivot'!$13:$13 = LARGE('Panel 2 Pivot'!$13:$13,1))`,
with the last argument of `LARGE` being 1, 2, and 3 to display the name
of the items that made the first, second, and third most revenue
respectively for the selected truck.

### Panel 3

Used
`=VLOOKUP(IF(Dashboard!$I$2='Data Validation'!$A$6,"Grand Total",Dashboard!$I$2),$A$3:$V$8,$B$17:$V$17,false)`
and
`=VLOOKUP(IF(Dashboard!$I$2='Data Validation'!$A$6,"Grand Total",Dashboard!$I$2),$A$12:$V$16,$B$17:$V$17,false)`
to find the average price, and quantity bought of each item for the
selected truck. Used these values to create a combo chart to visually
compare the correlation between average price and quantity bought.

### Panel 4

Visualization created by teammate. Double line chart that compares sales
quantity of the selected truck to the average sales quantity of all
trucks each day of the week.

### Panel 5

**Element 1:** Used
`=vlookup(If(Dashboard!$I$2="BGC (all)","Grand Total",Dashboard!$I$2),$G$1:$AB$8,H10,false)`
to find the profit margin of each item for the selected truck. Created a
column chart with scale up to 100%, essentially working as multiple
gauges. 

**Element 2:** Used
`=vlookup(If(Dashboard!$I$2="BGC (all)","Grand Total",Dashboard!$I$2),$E$1:$I$7,2,false)`
to find the profit margin of each group and truck specific menu for the
selected truck. Created a column chart with scale up to 100%,
essentially working as multiple gauges.

## 5. Key Points

**Panel 1** (Teammate) 
- Gross profit and revenue are highest in the months May to October, where all four trucks are running 
- At 70.6%, the truck uniDistFY is the only one that meets the gross profit target of 70% 
- Other trucks under target by 0.6% to 2.7% resulting in BGC’s overall gross profit margin also just below target at 69.2

**Panel 2**
- Main product category makes up bulk of revenues, 61% for BGC as a whole and from 56.80% to 66.86% for each of the trucks
- Hand-Cut Truffle Fries and Honey Bacon on Triple Cheese Melt appear in the top three list of product items that generate the most revenue for every truck and the overall company

**Panel 3** (Teammate)
- There is an inverse relationship between average item price and sales quantity. As the average item price rises, the quantity bought falls

**Panel 4** (Teammate) 
- Truck metroFY makes the highest quantity of sales on Friday 
- Even though specEventHY only operates Friday to Sunday, on its operational days it still sells below or similar to average
- On average, trucks make the highest quantity of sales on Monday

**Panel 5**
- Coffee is the item with highest profit margin, at 83.22%; followed by Redbull at 80.91%; and Polar Soda at 76.67%
- The items with the lowest profit margin of 61% are Strawberry Banana Juice, Double Fried Chicken Sandwich, Veggie Burger, and Vegan Veggie Burger
- Drink product group has highest profit margin for all trucks except for specEventHY

## 6. Recommendation

To increase BGC’s gross profit margin and meet the targeted 70% in the next fiscal year, BGC Management should take the following two actions:
- Remove Strawberry Banana Juice and promote other drinks, or reduce the costs. It is only offered by truck suburbHY. Though it is a popular item, the most sold at that truck, it is one of the items with the lowest profit margin. 
- Widen availability of Coffee. Currently only offered by truck metroFY, but has the highest profit margin of all BGC’s offerings.
