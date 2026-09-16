# E-Commerce Analytics Dashboard — Power BI

An end-to-end e-commerce analytics project built in **Microsoft Power BI** to analyze sales, customers, products, inventory, payments, shipments, returns, reviews, employees, and marketing activity.

The project uses a multi-table e-commerce dataset and focuses on **data cleaning, data modeling, DAX measures, KPI reporting, and interactive business analysis**.

## Project Overview

### Objective

The objective of this project is to turn raw e-commerce operational data into an interactive Power BI report that helps analyze:

- Overall business and sales performance
- Revenue and order trends
- Customer activity and purchasing behavior
- Product and category performance
- Delivery and shipment performance
- Inventory and stock levels
- Returns and customer reviews
- Marketing campaign information

The report is designed as a portfolio project to demonstrate practical **Power BI, data modeling, DAX, and analytical thinking**.

## Dataset

The project uses approximately **4.7 million rows across 12 CSV files**.

### Tables

| Table | Description |
|---|---|
| `categories` | Product category information |
| `customers` | Customer information |
| `employees` | Employee information |
| `inventory` | Inventory and stock information |
| `marketing_campaigns` | Marketing campaign information such as budget and channel |
| `order_items` | Individual products/items belonging to orders |
| `orders` | Order-level information including order date, customer, employee and status |
| `payments` | Payment information associated with orders |
| `products` | Product information |
| `returns` | Return information |
| `reviews` | Customer review information |
| `shipments` | Shipment, delivery and courier information |

### Important dataset scale

- `order_items`: approximately **1.475 million rows**
- `inventory`: **1 million+ rows**
- Total dataset: approximately **4.7 million rows**

Because the dataset is large, the project uses Power BI rather than trying to keep every table inside an Excel worksheet.

## Data Preparation

The data was checked and prepared before analysis.

Examples of data-quality issues identified during the project included:

- Missing values
- Duplicate records
- Invalid or inconsistent values
- Future/invalid order dates
- `quantity` values of `0` and negative values in `order_items`
- Negative product/item price values, including `-50`
- Date/time and data-type issues in shipment date fields

The goal of the cleaning stage was to make the data suitable for relationships, calculations and reporting while avoiding unnecessary merging of all source tables.

## Data Model

The report uses separate related tables rather than merging the entire dataset into one large table.

Important relationships established in the model include:

```text
customers[customer_id]
        1
        |
        *
orders[customer_id]


products[product_id]
        1
        |
        *
order_items[product_id]
```

An order-date dimension was also used for time-based reporting:

```text
DateTable[Date]
        1
        |
        *
orders[order_date]
```

The model was built with single-direction relationships where appropriate.

## Power BI Report Pages

The report is organized around business areas.

### 1. Executive Overview

Provides a high-level view of overall business performance.

Typical KPIs include:

- Total Revenue
- Total Orders
- Customer metrics
- Inventory metrics
- Delivery/operations metrics

The purpose of this page is to give a business user a quick overview before moving into detailed analysis.

### 2. Sales Analysis

Focuses on sales performance and trends.

The page includes analysis such as:

- Revenue trends over time
- Monthly sales/revenue
- Year-over-year revenue growth
- Product/category performance
- Sales performance across available business dimensions

A date dimension is used for time-based analysis.

### 3. Customer Analysis

Focuses on customer activity and behavior.

The analysis can be used to understand:

- Customer counts
- Customer purchasing activity
- Customer-related sales performance
- Customer behavior across available dimensions

### 4. Operations Analysis

Focuses on order fulfillment and shipment performance.

The analysis includes areas such as:

- Delivery time by courier
- Delivery performance
- Late deliveries
- Promised versus actual delivery dates
- Shipping-related performance

A late delivery is based on the relationship between the actual delivery date and promised delivery date.

### 5. Inventory Analysis

Focuses on stock availability and inventory performance.

The analysis includes:

- Closing stock
- Out-of-stock products
- Inventory turnover
- Stock status
- Product-level inventory performance

For the out-of-stock KPI, a closing-stock value of **0** means there are zero units remaining at the relevant closing-stock level.

## Key DAX Measures

The project uses DAX measures for KPI calculations and time-based analysis.

### Total Revenue

```DAX
Total Revenue =
SUM(order_items[net_amount])
```

### Revenue Previous Year

```DAX
Revenue Previous Year =
CALCULATE(
    [Total Revenue],
    SAMEPERIODLASTYEAR('DIM DATE'[Date])
)
```

### Revenue YoY Growth %

```DAX
Revenue YoY Growth % =
DIVIDE(
    [Total Revenue] - [Revenue Previous Year],
    [Revenue Previous Year],
    0
)
```

### Inventory Turnover

```DAX
Inventory Turnover =
DIVIDE(
    [Inventory Units Sold],
    [Closing Stock],
    0
)
```

Inventory turnover is a **ratio**, not a percentage. For example, `0.42` represents a turnover ratio of `0.42x`.

### Out of Stock Products

```DAX
Out of Stock Products =
CALCULATE(
    DISTINCTCOUNT(inventory[product_id]),
    inventory[closing_stock] = 0
)
```

This counts distinct products whose `closing_stock` is zero.

> Note: DAX shown above reflects the measures documented during the project work. Additional measures may exist in the final PBIX file.

## Visual Analysis

Examples of visuals developed/planned in the report include:

- KPI cards
- Line charts for revenue trends
- YoY revenue analysis
- Courier delivery-time comparison
- Inventory KPI cards
- Stock-status analysis
- Product/category analysis
- Customer analysis
- Operational delivery analysis

## Business Questions

The dashboard is designed to help answer questions such as:

1. How is revenue changing over time?
2. How many orders are being generated?
3. How is revenue changing compared with the previous year?
4. Which products or categories contribute to sales?
5. How are customers performing across the business?
6. Which couriers have different delivery-time patterns?
7. How many products are out of stock?
8. What is the inventory turnover ratio?
9. How are promised and actual delivery dates related to late deliveries?
10. What patterns can be identified across sales, inventory and operations?

## Tools & Technologies

- **Microsoft Power BI**
- **Power Query** — data preparation and transformation
- **DAX** — measures and KPI calculations
- **CSV** — source data
- **Data modeling / relationships** — multi-table analytical model

## Data Modeling Approach

The project intentionally keeps major business entities in separate tables.

This makes it possible to analyze different business processes without unnecessarily combining millions of rows into one table.

Examples:

```text
Customers → Orders → Order Items → Products
                    |
                    └── Payments

Orders → Shipments
Products → Inventory
Orders/Products → Returns / Reviews
Marketing Campaigns → Campaign analysis
Employees → Order/employee analysis
```

The exact relationship paths and cardinalities should be verified against the final PBIX model if the model is changed after this documentation was prepared.

## Repository Structure

```text
ecommerce-powerbi-dashboard/
|
|── Dataset/
|  |── categories.csv
|  |── customers.csv
|  |── employees.csv
|  |── inventory.csv
|  |── marketing_campaigns.csv
|  |── order_items.csv
|  |── orders.csv
|  |── payments.csv
|  |── products.csv
|  |── returns.csv
|  |── reviews.csv
|  |── shipments.csv
|
|── E-Commerce Analytics Dashboard.pdf
│
|── Project_Report.pdf
│   
│
|── README.md



## Author

**Jenil Aapa**

BCA Graduate | Data Analytics

Skills demonstrated in this project:

**Power BI • DAX • Power Query • Data Modeling • Data Cleaning • KPI Analysis • Data Visualization • Business Analysis**
