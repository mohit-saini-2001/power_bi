# Sales Analytics Dashboard – Power BI

## Overview
An interactive Power BI dashboard built on the **Classic Models** sample dataset, analyzing sales performance across 2003–2005. It provides a consolidated view of revenue trends, top customers, best-selling products, and order volumes.

## Dashboard Preview
The dashboard includes the following visuals:

- **Revenue By Year** – Pie chart showing yearly revenue split (2003, 2004, 2005), with 2004 contributing the largest share (48.72%, ₹4.31M).
- **Top 5 Customers By Revenue** – Bar chart ranking customers by purchase value (Diego Freyre leading at ₹0.72M).
- **Top 5 Customers By Order Count** – Table showing FullName vs Purchase Count.
- **Revenue By Month For Every Year** – Line chart comparing monthly revenue trends across the three years.
- **Top 15 Selling Products** – Table ranking products by sales count (e.g., 1982 Camaro Z28, ATA: B757-300).
- **Order Count By Year** – Donut chart breaking down total orders by year.
- **KPI Cards** – Total Revenue (₹8.85M) and Total Number of Orders (4,142).
- **Year Slicer** – Checkbox filter (2003/2004/2005) to dynamically filter all visuals.

## Data Model
The report is built on a relational data model with the following tables:
- **Orders** & **Order Details** – transactional sales data (order dates, quantities, prices, revenue calculations)
- **Product** & **Product Line** – product catalog details (code, name, vendor, stock)
- **Customers** – customer master data (name, address, credit limit, rank)
- **Employee** & **Office** – sales rep and office location details
- **Payment** – payment transactions per customer
- **DateTable** – custom date dimension table (Year, Quarter, Month) used for time-based analysis

Relationships are established between Orders, Order Details, Products, Customers, Payments, and the Date table to enable cross-filtering across all visuals.

## Key Measures (DAX)
Based on the visible fields, the model includes calculated measures such as:
- Total Price / Profit (Order Details)
- Total by Customer (Payment)
- Order & Customer Count (Orders)
- Year / Month Number / Quarter (DateTable)

## Tech Stack
- **Tool:** Microsoft Power BI Desktop
- **Data Source:** Classic Models sample dataset (Excel/CSV/Database)
- **Features Used:** Relationships, Calculated Columns & Measures, Slicers, DAX

## Notes
> ⚠️ The original `.pbix` file was lost. This README was reconstructed from two saved screenshots (dashboard view and data model view) to document the project structure for future reference.

## How to Rebuild

### 1. Import Data
Load the Classic Models tables (Orders, Order Details, Customers, Product, Product Line, Employee, Office, Payment) into Power BI via **Get Data**.

### 2. Create a Date Table
```DAX
DateTable = CALENDAR(MIN(Orders[orderDate]), MAX(Orders[orderDate]))

Year = YEAR(DateTable[Date])
Month = FORMAT(DateTable[Date], "MMMM")
Month Number = MONTH(DateTable[Date])
Quarter = "Q" & FORMAT(DateTable[Date], "Q")
```

### 3. Build Relationships
- Orders[orderNumber] → Order Details[orderNumber] (1 to many)
- Orders[customerNumber] → Customers[customerNumber] (1 to many)
- Order Details[productCode] → Product[productCode] (1 to many)
- Orders[orderDate] → DateTable[Date] (1 to many)
- Payment[customerNumber] → Customers[customerNumber] (1 to many)

### 4. Key Measures
```DAX
Total Price = SUMX(Order Details, Order Details[priceEach] * Order Details[quantityOrdered])

Profit = SUMX(Order Details, (Order Details[priceEach] - RELATED(Product[buyPrice])) * Order Details[quantityOrdered])

Total Revenue = SUM(Order Details[Total Price])

Total Number of Orders = DISTINCTCOUNT(Orders[orderNumber])

Total by Customer = SUM(Payment[amount])

Order Count By Year = CALCULATE(DISTINCTCOUNT(Orders[orderNumber]), ALLEXCEPT(DateTable, DateTable[Year]))
```

### 5. Build Visuals
| Visual | Chart Type | Fields |
|---|---|---|
| Revenue By Year | Pie Chart | DateTable[Year], [Total Revenue] |
| Top 5 Customers By Revenue | Bar Chart | Customers[Name], [Total Revenue] (Top N filter = 5) |
| Top 5 Customers By Order | Table | Customers[FullName], [Purchase Count] |
| Revenue By Month For Every Year | Line Chart | DateTable[Month], DateTable[Year] (legend), [Total Revenue] |
| Top 15 Selling Products | Table | Product[productName], [Sales Count] (Top N filter = 15) |
| Order Count By Year | Donut Chart | DateTable[Year], [Order Count By Year] |
| KPI Cards | Card | [Total Revenue], [Total Number of Orders] |
| Year Slicer | Slicer (checkbox) | DateTable[Year] |

### 6. Formatting
- Apply a dark purple/maroon theme background
- Use blue/orange accent colors for chart series (2003 = light blue, 2004 = dark blue, 2005 = orange)
- Format currency fields with ₹ symbol and M (millions) unit

## Possible Improvements
- Add drill-through pages for customer/product deep-dive
- Add YoY growth % measures
- Add conditional formatting to highlight top performers
- Publish to Power BI Service for sharing and scheduled refresh
