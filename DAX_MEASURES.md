# DAX Measures — E-Commerce Analytics Dashboard

This file contains the main DAX measures and calculated columns used in the E-Commerce Analytics Dashboard.

---

## 1. Date Table

### DateTable

```DAX
DateTable =
CALENDAR(
    MIN(orders[order_date]),
    MAX(orders[order_date])
)
```

### DateTable Calculated Columns

```DAX
Year =
YEAR(DateTable[Date])
```

```DAX
Month Number =
MONTH(DateTable[Date])
```

```DAX
Month =
FORMAT(DateTable[Date], "MMM")
```

```DAX
Year Month =
FORMAT(DateTable[Date], "YYYY-MM")
```

```DAX
Quarter =
"Q" & FORMAT(DateTable[Date], "Q")
```

**Date relationship:**

`DateTable[Date]` → `orders[order_date]`

* Cardinality: One-to-many (1:*)
* Cross-filter direction: Single
* Active relationship: Yes

**Important:** Use `DateTable[Date]` for time-intelligence calculations instead of the automatic Date Hierarchy.

For correct month sorting:

`Month` → **Sort by column** → `Month Number`

---

# 2. Sales & Revenue

### Total Revenue

```DAX
Total Revenue =
SUM(order_items[net_amount])
```

### Gross Sales

```DAX
Gross Sales =
SUM(order_items[gross_amount])
```

### Total Discount

```DAX
Total Discount =
SUM(order_items[discount_amount])
```

### Average Discount %

```DAX
Average Discount % =
AVERAGE(order_items[discount_pct])
```

### Total Tax

```DAX
Total Tax =
SUM(order_items[tax_amount])
```

### Average Tax %

```DAX
Average Tax % =
AVERAGE(order_items[tax_pct])
```

### Net Revenue

```DAX
Net Revenue =
SUM(order_items[net_amount])
```

---

# 3. Cost & Profit

### Total Cost

```DAX
Total Cost =
SUMX(
    order_items,
    order_items[quantity] * RELATED(products[unit_cost])
)
```

### Gross Profit

```DAX
Gross Profit =
[Total Revenue] - [Total Cost]
```

### Profit Margin %

```DAX
Profit Margin % =
DIVIDE(
    [Gross Profit],
    [Total Revenue],
    0
)
```

---

# 4. Orders & Sales Volume

### Total Orders

```DAX
Total Orders =
DISTINCTCOUNT(orders[order_id])
```

### Total Quantity Sold

```DAX
Total Quantity Sold =
SUM(order_items[quantity])
```

### Average Order Value

```DAX
Average Order Value =
DIVIDE(
    [Total Revenue],
    [Total Orders],
    0
)
```

### Units Per Order

```DAX
Units Per Order =
DIVIDE(
    [Total Quantity Sold],
    [Total Orders],
    0
)
```

### Profit Per Order

```DAX
Profit Per Order =
DIVIDE(
    [Gross Profit],
    [Total Orders],
    0
)
```

### Profit Per Unit

```DAX
Profit Per Unit =
DIVIDE(
    [Gross Profit],
    [Total Quantity Sold],
    0
)
```

---

# 5. Order Status

### Completed Orders

```DAX
Completed Orders =
CALCULATE(
    [Total Orders],
    orders[order_status] = "Completed"
)
```

### Cancelled Orders

```DAX
Cancelled Orders =
CALCULATE(
    [Total Orders],
    orders[order_status] = "Cancelled"
)
```

### Cancellation Rate %

```DAX
Cancellation Rate % =
DIVIDE(
    [Cancelled Orders],
    [Total Orders],
    0
)
```

---

# 6. Returns & Refunds

### Returned Orders

```DAX
Returned Orders =
DISTINCTCOUNT(returns[order_id])
```

### Total Returns

```DAX
Total Returns =
COUNTROWS(returns)
```

### Total Refund Amount

```DAX
Total Refund Amount =
SUM(returns[refund_amount])
```

### Return Rate %

```DAX
Return Rate % =
DIVIDE(
    [Returned Orders],
    [Total Orders],
    0
)
```

### Refund Rate %

```DAX
Refund Rate % =
DIVIDE(
    [Total Refund Amount],
    [Total Revenue],
    0
)
```

---

# 7. Customer Analytics

### Total Customers

```DAX
Total Customers =
DISTINCTCOUNT(customers[customer_id])
```

### Revenue Per Customer

```DAX
Revenue Per Customer =
DIVIDE(
    [Total Revenue],
    [Total Customers],
    0
)
```

### Orders Per Customer

```DAX
Orders Per Customer =
DIVIDE(
    [Total Orders],
    [Total Customers],
    0
)
```

### New Customers

```DAX
New Customers =
DISTINCTCOUNT(customers[customer_id])
```

> Note: This measure represents new customers correctly only when the date filter is connected to `customers[signup_date]`.

### Repeat Customers

```DAX
Repeat Customers =
COUNTROWS(
    FILTER(
        VALUES(customers[customer_id]),
        CALCULATE(
            DISTINCTCOUNT(orders[order_id])
        ) > 1
    )
)
```

### Repeat Customer %

```DAX
Repeat Customer % =
DIVIDE(
    [Repeat Customers],
    [Total Customers],
    0
)
```

---

# 8. Product Analytics

### Total Products

```DAX
Total Products =
DISTINCTCOUNT(products[product_id])
```

### Active Products

If `active_flag` is Boolean:

```DAX
Active Products =
CALCULATE(
    DISTINCTCOUNT(products[product_id]),
    products[active_flag] = TRUE()
)
```

If `active_flag` contains `"Y"`:

```DAX
Active Products =
CALCULATE(
    DISTINCTCOUNT(products[product_id]),
    products[active_flag] = "Y"
)
```

---

# 9. Time Intelligence

### Revenue YTD

```DAX
Revenue YTD =
TOTALYTD(
    [Total Revenue],
    DateTable[Date]
)
```

### Revenue MTD

```DAX
Revenue MTD =
TOTALMTD(
    [Total Revenue],
    DateTable[Date]
)
```

### Revenue Previous Year

```DAX
Revenue Previous Year =
CALCULATE(
    [Total Revenue],
    SAMEPERIODLASTYEAR(DateTable[Date])
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

### Revenue Previous Month

```DAX
Revenue Previous Month =
CALCULATE(
    [Total Revenue],
    DATEADD(
        DateTable[Date],
        -1,
        MONTH
    )
)
```

### Revenue MoM Growth %

```DAX
Revenue MoM Growth % =
DIVIDE(
    [Total Revenue] - [Revenue Previous Month],
    [Revenue Previous Month],
    0
)
```

---

# 10. Shipping & Operations

### Total Shipping Cost

```DAX
Total Shipping Cost =
SUM(shipments[shipping_cost])
```

### Average Shipping Cost

```DAX
Average Shipping Cost =
AVERAGE(shipments[shipping_cost])
```

### Average Delivery Days

```DAX
Average Delivery Days =
AVERAGE(shipments[delivery_days])
```

### Late Deliveries

```DAX
Late Deliveries =
CALCULATE(
    COUNTROWS(shipments),
    shipments[actual_delivery_date]
        > shipments[promised_delivery_date]
)
```

### Late Delivery Rate %

```DAX
Late Delivery Rate % =
DIVIDE(
    [Late Deliveries],
    COUNTROWS(shipments),
    0
)
```

### On Time Deliveries

```DAX
On Time Deliveries =
CALCULATE(
    COUNTROWS(shipments),
    shipments[actual_delivery_date]
        <= shipments[promised_delivery_date]
)
```

### On Time Delivery %

```DAX
On Time Delivery % =
DIVIDE(
    [On Time Deliveries],
    COUNTROWS(shipments),
    0
)
```

---

# 11. Payments

### Total Payment Amount

```DAX
Total Payment Amount =
SUM(payments[amount])
```

### Successful Payments

```DAX
Successful Payments =
CALCULATE(
    COUNTROWS(payments),
    payments[payment_status] = "Success"
)
```

### Failed Payments

```DAX
Failed Payments =
CALCULATE(
    COUNTROWS(payments),
    payments[payment_status] = "Failed"
)
```

### Payment Success Rate %

```DAX
Payment Success Rate % =
DIVIDE(
    [Successful Payments],
    COUNTROWS(payments),
    0
)
```

### Payment Failure Rate %

```DAX
Payment Failure Rate % =
DIVIDE(
    [Failed Payments],
    COUNTROWS(payments),
    0
)
```

---

# 12. Inventory

### Closing Stock

```DAX
Closing Stock =
SUM(inventory[closing_stock])
```

### Opening Stock

```DAX
Opening Stock =
SUM(inventory[opening_stock])
```

### Units Received

```DAX
Units Received =
SUM(inventory[units_received])
```

### Inventory Units Sold

```DAX
Inventory Units Sold =
SUM(inventory[units_sold])
```

### Inventory Value

```DAX
Inventory Value =
SUMX(
    inventory,
    inventory[closing_stock] * inventory[unit_cost]
)
```

### Low Stock Products

```DAX
Low Stock Products =
CALCULATE(
    DISTINCTCOUNT(inventory[product_id]),
    inventory[closing_stock] <= inventory[reorder_level]
)
```

### Out of Stock Products

```DAX
Out of Stock Products =
CALCULATE(
    DISTINCTCOUNT(inventory[product_id]),
    inventory[closing_stock] = 0
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

---

# 13. Marketing

### Total Marketing Budget

```DAX
Total Marketing Budget =
SUM(marketing_campaigns[budget])
```

### Marketing Spend

```DAX
Marketing Spend =
SUM(marketing_campaigns[budget])
```

> Note: Use `Marketing Spend` only if the `budget` field represents actual marketing spend. If it represents planned budget, keep the name `Total Marketing Budget`.

### Campaign Revenue

```DAX
Campaign Revenue =
[Total Revenue]
```

### ROAS

```DAX
ROAS =
DIVIDE(
    [Campaign Revenue],
    [Marketing Spend],
    0
)
```

### Marketing ROI %

```DAX
Marketing ROI % =
DIVIDE(
    [Campaign Revenue] - [Marketing Spend],
    [Marketing Spend],
    0
)
```

---

# 14. Reviews

### Total Reviews

```DAX
Total Reviews =
COUNTROWS(reviews)
```

### Average Rating

```DAX
Average Rating =
AVERAGE(reviews[rating])
```

### Five Star Reviews

```DAX
Five Star Reviews =
CALCULATE(
    COUNTROWS(reviews),
    reviews[rating] = 5
)
```

### Positive Reviews

```DAX
Positive Reviews =
CALCULATE(
    COUNTROWS(reviews),
    reviews[rating] >= 4
)
```

### Positive Review %

```DAX
Positive Review % =
DIVIDE(
    [Positive Reviews],
    [Total Reviews],
    0
)
```

### Verified Reviews

If `verified_purchase` is Boolean:

```DAX
Verified Reviews =
CALCULATE(
    COUNTROWS(reviews),
    reviews[verified_purchase] = TRUE()
)
```

If `verified_purchase` contains `"Yes"`:

```DAX
Verified Reviews =
CALCULATE(
    COUNTROWS(reviews),
    reviews[verified_purchase] = "Yes"
)
```

---

# 15. Employee Analytics

### Total Employees

```DAX
Total Employees =
DISTINCTCOUNT(employees[employee_id])
```

### Total Salary

```DAX
Total Salary =
SUM(employees[salary])
```

### Average Salary

```DAX
Average Salary =
AVERAGE(employees[salary])
```

### Active Employees

```DAX
Active Employees =
CALCULATE(
    DISTINCTCOUNT(employees[employee_id]),
    employees[employment_status] = "Active"
)
```

### Orders Per Employee

```DAX
Orders Per Employee =
[Total Orders]
```

---

# Notes

* Table and column names must exactly match the Power BI model.
* Status values such as `"Completed"`, `"Cancelled"`, `"Success"`, `"Failed"`, and `"Active"` must match the actual dataset values.
* `active_flag` and `verified_purchase` can be either Boolean or text depending on the dataset. Use the appropriate version.
* All time-intelligence calculations use `DateTable[Date]`.
* The `DateTable` relationship should be active and connected to `orders[order_date]`.
* For monthly visuals, use `DateTable[Year Month]`.
* The `Month` column should be sorted by `Month Number`.
* Marketing ROI and ROAS should only be used when the marketing spend/budget field is appropriate for the calculation.
* Inventory turnover shown here is a simplified calculation using units sold divided by closing stock.
