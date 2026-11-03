# Coffee Shop Sales Dashboard (Power BI + SQL)

A sales dashboard I built in Power BI using six months of transaction data from a coffee shop chain with three stores in New York. I wanted to answer the usual questions a store manager would ask: how are we doing this month compared to last month, which products carry the business, and when are people actually walking in?

I also used SQL (MySQL) to clean the data and write the same KPIs and breakdowns as queries, so the project covers both the SQL side and the dashboard side.

The report file is `COFFEE_SHOP_SALES.pbix`. Open it in Power BI Desktop and everything is interactive.

![Coffee Shop Sales Dashboard](Screenshot%202026-11-02%20233310.png)

## The data

The source is a single Excel workbook (`Coffee Shop Sales.xlsx`) with one sheet called `Transactions`.

- **149,116 transactions**, one row per order line
- **1 Jan 2023 to 30 Jun 2023**
- **3 stores:** Hell's Kitchen, Astoria, Lower Manhattan
- **9 product categories**, 29 product types, 80 individual products
- Columns include transaction date and time, quantity, store, product details and unit price

Loaded through Power Query. I only had to fix the data types (dates, whole numbers, decimals), the data itself was already clean.

## What's in the dashboard

**Main page**

- Three KPI cards: total sales, total orders and total quantity sold
- Each card shows the change against last month, with an up or down arrow, so you can see at a glance if the month is going well
- A month slicer that filters the whole page
- Daily sales column chart. Bars are coloured depending on whether that day beat the daily average or fell below it
- Sales by store location
- Sales by product category and by product type
- Weekday vs weekend split (donut chart)
- Sales by day of the week
- Sales by hour of the day, plus a day-by-hour table to spot the busy slots
- A calendar-style table of days and weeks

**Tooltip pages**

Two hidden tooltip pages show extra detail when you hover. One gives a mini breakdown of sales, orders and quantity for the calendar view, and the other does the same for the day and hour charts. There's a small "Hover on this visual to see details" note on the page so people know to try it.

## How it's built

The model is simple: one fact table (`Transactions`) and a calendar table (`DAY TABLE`) that I generated in DAX from the min and max transaction dates.

Some things I added on top of the raw data:

- `SALES` column = unit price x quantity
- `HOUR` column pulled out of the transaction time
- Calendar columns: month, month-year, day name, week number, and a weekday/weekend flag

**Main measures**

| Measure | What it does |
|---|---|
| `TOTAL SALES` | Sum of the sales column |
| `total orders` | Distinct count of transaction IDs |
| `TOTAL QTY SOLD` | Sum of quantity |
| `CM Sales` / `PM Sales` | Current month to date vs previous month, using `TOTALMTD` and `DATEADD` |
| `MOM Growth & Diff Sales` | Builds the label like "▲ +6.2% \| +9.8k vs LM". Same idea repeated for orders and quantity |
| `DAILY AVG SALES` | Average sales per day in the current selection |
| `COLOUR FOR BARS` | Tags each day as above or below the average, used for the conditional colouring |

I also wrote a few small label measures so the bar charts show things like "Astoria | $232.24K" instead of just a bare number.

## SQL queries

I also used SQL (MySQL) in this project, both to clean the data and to work out the same KPIs that show up in the dashboard. All the queries are uploaded in this repo: [`sql/coffee_shop_queries.sql`](sql/coffee_shop_queries.sql)

What they cover:

- Cleaning the date and time columns and fixing a broken column name from the CSV import
- Total sales, total orders and total quantity sold, with month-over-month growth
- Daily sales, and a check of which days were above or below the average
- Weekday vs weekend sales
- Sales by store, product category and top 10 product types
- Sales by day of the week and by hour of the day

## What the numbers say

A few things that stood out when I went through it (all based on the data in this file):

- **Total sales: about $698.8K** from roughly 214K items sold
- **Growth was big.** January was around $81.7K and June reached about $166.5K, which is roughly double in six months. June was up around 6% on May
- **The three stores are very close.** Hell's Kitchen is slightly ahead at about $236.5K, then Astoria ($232.2K) and Lower Manhattan ($230.1K)
- **Coffee is the biggest category** ($270K), but tea is a strong second ($196K). Together with bakery and drinking chocolate they make up the bulk of revenue
- **Barista Espresso is the top product type** by sales, with brewed chai tea right behind it
- **Mornings are the rush.** 10 AM is the busiest hour, followed by 9 AM and 8 AM
- **Weekdays carry most of the revenue**, which makes sense for a commuter-heavy part of the city. Daily sales are pretty even across the week though

## Tools

- Power BI Desktop
- DAX
- Power Query
- MySQL
- Excel (data source)

## How to run it

**Power BI report**

1. Download `COFFEE_SHOP_SALES.pbix` from this repo
2. Open it in Power BI Desktop
3. If Power BI complains about the data source, go to **Transform data > Data source settings** and point it to your local copy of `Coffee Shop Sales.xlsx`. The path in my file is hard-coded to my own machine, so this step is needed

**SQL queries**

1. Import the sales data into a MySQL table called `coffee_shop_sales`
2. Run the cleaning queries first, so the date and time columns have the right types
3. Run the other queries one at a time

## Things I'd like to add

- Profit or margin if cost data is ever available
- A product-level drill-through page
- Forecasting for the next few months
- Year-over-year comparison once there's more than six months of data

## About me

Built by **VEDANT SHARMA**. I'm learning data analytics and this was one of my practice projects.

- LinkedIn: [Vedant Sharma](https://www.linkedin.com/in/vedant-sharma-491847410/)

Feedback is welcome. If you spot something I could have done better, open an issue or just message me.
