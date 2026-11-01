# Intermediate-Assessment---Codeathon
# Sales Data Cleaning and Profit Analysis

*A Power BI assessment project using Power Query, DAX and report visualizations.*

## Problem Description

The dataset contains customer orders with missing values and duplicate records. The objective was to clean the data, standardize dates, analyse regional orders and product popularity, and display profit trends.

## Tools Used

- **Power BI Desktop:** data modelling and report creation.
- **Power Query Editor:** data cleaning and transformation.
- **DAX:** calculated tables, columns and measures.

## Data Cleaning and Methods Used

1. Imported the CSV through **Get Data → Text/CSV → Transform Data**.
2. Promoted headers and assigned appropriate text, number and date types.
3. Trimmed extra spaces from text columns.
4. Recovered missing numeric values using the formulas below.
5. Replaced missing customer names, regions and products with **Unknown**.
6. Converted **OrderDate** to Date and recovered one missing date from its matching duplicate. Left the unrecoverable date blank.
7. Removed the original columns after creating their corrected replacements, then renamed and reordered the fields.
8. Removed duplicate rows and loaded the cleaned table as **SalesData** using **Close & Apply**.

**Column replacements:** ~~UnitPriceFixed~~ → **Unit Price** and ~~OrderDate Fixed~~ → **OrderDate**. Other temporary repair columns were similarly renamed after replacing their originals.

### Missing Value Formulas

The following relationships were applied through conditional custom columns, preserving existing values:

- **Unit Price = Sales / Quantity**
- **Sales = Quantity × Unit Price**
- **Cost = Sales − Profit**
- **Profit = Sales − Cost**

Example Power Query formula:

```powerquery
if [Profit] = null then [Sales] - [Cost] else [Profit]
```

For missing text, the same pattern was applied to customer names, regions and products:

```powerquery
if [Region] = null or [Region] = ""
then "Unknown"
else [Region]
```

## DAX Formulas Used

### EastRegionOrders — Calculated Table

```dax
EastRegionOrders = FILTER(SalesData, SalesData[Region] = "East")
```

Creates a separate table containing only East-region orders.

### ProfitCostDifference — Calculated Column

```dax
ProfitCostDifference = SalesData[Profit] - SalesData[Cost]
```

Calculates the difference between Profit and Cost for each order.

### TotalProfit — Measure

```dax
TotalProfit = SUM(SalesData[Profit])
```

Calculates total profit within the current filter context.

### Additional Supporting Calculations

```dax
OrderCount = COUNTROWS(SalesData)
```

Counts orders for the pie chart, column chart and Top 5 filter.

```dax
OrderYear =
IF(
    ISBLANK(SalesData[OrderDate]),
    BLANK(),
    YEAR(SalesData[OrderDate])
)
```

Extracts the year for the profit trend while keeping missing dates blank.

## Report Pages and Filters

### Pie Chart — Order Distribution by Region

- **Fields:** Region and OrderCount.
- **Method:** compared regional order counts using a pie chart.
- **Filter:** selected East, North, South and West; Unknown remains in the model but is excluded from this visual.
- **Presentation:** category legend, order counts and percentage labels.

### Column Chart — Top 5 Trending Products

- **Fields:** Product and OrderCount.
- **Method:** compared how often products were ordered using a clustered column chart.
- **Filter:** Top N → Top 5, ranked by OrderCount.
- **Sorting:** OrderCount descending.

### Line Chart — Profit Trend Over Time

- **Fields:** OrderYear and Sum of Profit.
- **Method:** grouped profit by year to show changes over time.
- **Calculation:** the saved chart uses Sum of Profit; the separate TotalProfit measure is also available.
- **Interpretation:** the undated order remains in overall totals, and the final year contains only partial-year data.

## Font and Report Formatting

- Used **Times New Roman** for chart titles, axes, pie labels and legend.
- Applied bold titles at size **18** and configured labels at size **13**.
- Used distinct colours for regional slices and blue for the columns and profit line.
- Added descriptive titles, readable axes and a light background.
- Organized the report into **Pie Chart**, **Column Chart** and **Line Chart** pages using **Fit to page**.
- Used fixed-decimal monetary fields. The displayed rupee symbol is a formatting choice; the source does not specify a currency.
- Kept OrderDate as a Date field; its saved model display format is **Long Date**.
