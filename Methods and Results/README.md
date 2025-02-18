![Supply_Chain](/Assets/supply_chain.jpg)

In this project, we conducted a comprehensive analysis of a company's supply chain and sales data. The goal was to uncover key insights that could help optimize operations, improve profitability, and enhance decision-making. Below, we present a series of questions that guided our analysis, along with the SQL queries used to answer them.

We first begin by creating the database and table in which we will be able to import our public data into. 

```sql
DROP DATABASE IF EXISTS SupplyChain;
CREATE DATABASE SupplyChain;
USE SupplyChain;

CREATE TABLE Data_Source(
    Product_type VARCHAR(255),
    SKU VARCHAR(255),
    Price FLOAT,
    Availability INT,
    Number_Of_Products_Sold INT,
    Revenue_Generated FLOAT,
    Customer_Demographics VARCHAR(255),
    Stock_Levels INT,
    Lead_Times INT,
    Order_Quantities INT,
    Shipping_Times INT,
    Shipping_Carriers VARCHAR(255),
    Shipping_Cost FLOAT,
    Supplier_Name VARCHAR(255),
    Location VARCHAR(255),
    Lead_Time INT,
    Production_Volume INT,
    Manufacturing_Lead_Time INT,
    Manufactturing_Cost FLOAT,
    Inspection_Results VARCHAR(255),
    Defect_Rate FLOAT,
    Transportation_Modes VARCHAR(255),
    Routes VARCHAR(255),
    Costs FLOAT
);
```


To understand the company's product portfolio, we analyzed the number of products by type and calculated the average price for each category.
```sql
SELECT Product_type,
    COUNT(Product_type) AS Number_of_Products,
    ROUND(AVG(Price), 2) AS Average_Price
FROM Data_Source
GROUP BY Product_type
ORDER BY 2 DESC;
```
| Product_type | Number_of_Products | Average_Price |
| ------------ | ------------------ | ------------- |
| skincare | 40 | 47.26 |
| haircare | 34 | 46.01 |
| cosmetics | 26 | 57.36 |


We identified the top-performing products based on the total quantities sold. This helps in recognizing which products are most popular among customers.
```sql
SELECT Product_Type,
    SKU,
    SUM(Order_Quantities) AS '# of Orders'
FROM Data_Source
GROUP BY Product_Type, SKU
ORDER BY 3 DESC
LIMIT 10;
```
| Product_Type | SKU | # of Orders |
| ------------ | --- | ----------- |
| haircare | SKU0 | 96 |
| skincare | SKU90 | 96 |
| cosmetics | SKU33 | 95 |
| skincare | SKU19 | 94 |
| haircare | SKU2 | 88 |
| cosmetics | SKU38 | 88 |
| haircare | SKU43 | 85 |
| cosmetics | SKU17 | 85 |
| cosmetics | SKU35 | 85 |
| haircare | SKU12 | 85 |


Conversely, we also looked at the least sold products to identify potential underperformers that may require attention.
```sql
SELECT Product_Type,
    SKU,
    SUM(Order_Quantities) AS '# of Orders'
FROM Data_Source
GROUP BY Product_Type, SKU
ORDER BY 3 ASC
LIMIT 10;
```
| Product_Type | SKU | # of Orders |
| ------------ | --- | ----------- |
| haircare | SKU74 | 1 |
| haircare | SKU24 | 2 |
| haircare | SKU97 | 4 |
| haircare | SKU46 | 6 |
| haircare | SKU83 | 7 |
| cosmetics | SKU21 | 7 |
| haircare | SKU81 | 8 |
| haircare | SKU48 | 9 |
| cosmetics | SKU49 | 9 |
| cosmetics | SKU92 | 10 |


We analyzed sales performance across different locations to understand regional contributions to total revenue.
```sql
SELECT Location,
    Product_type,
    ROUND(SUM(Revenue_generated), 2) AS Total_Revenue,
    SUM(Order_Quantities) AS '# of Orders'
FROM Data_Source
GROUP BY Location, Product_type
ORDER BY Total_Revenue DESC;
```
| Location | Product_type | Total_Revenue | # of Orders |
| -------- | ------------ | ------------- | ----------- |
| Kolkata | skincare | 77886.27 | 644 |
| Chennai | skincare | 58957.42 | 472 |
| Bangalore | haircare | 51654.35 | 297 |
| Mumbai | cosmetics | 49156.51 | 356 |
| Mumbai | haircare | 44423.98 | 359 |
| Mumbai | skincare | 44174.54 | 368 |
| Delhi | cosmetics | 37429.68 | 405 |
| Kolkata | haircare | 35027.71 | 435 |
| Bangalore | skincare | 31637.82 | 358 |
| Chennai | cosmetics | 31461.95 | 319 |
| Delhi | skincare | 28972.12 | 257 |
| Chennai | haircare | 28723.45 | 318 |
| Kolkata | cosmetics | 24163.57 | 149 |
| Bangalore | cosmetics | 19309.56 | 114 |
| Delhi | haircare | 14625.9 | 71 |


Understanding lead times is crucial for supply chain efficiency. We calculated the average lead time for each supplier to identify potential bottlenecks.
```sql
SELECT Supplier_Name,
    ROUND(AVG(Lead_Times), 2) AS Average_Lead_Times,
    ROUND(AVG(Lead_Time), 2) AS Average_Lead_Time
FROM Data_Source
GROUP BY Supplier_Name
ORDER BY Supplier_Name;
```
| Supplier_Name | Average_Lead_Times | Average_Lead_Time |
| ------------- | ------------------ | ----------------- |
| Supplier 1 | 16.78 | 14.78 |
| Supplier 2 | 16.23 | 18.55 |
| Supplier 3 | 14.33 | 20.13 |
| Supplier 4 | 17.00 | 15.22 |
| Supplier 5 | 14.72 | 18.06 |


We broke down revenue by product type to identify which categories are the most profitable.
```sql
SELECT Product_Type,
    ROUND(SUM(Revenue_generated), 2) AS Total_Revenue,
    ROUND(SUM(Revenue_Generated) - SUM(Costs), 2) AS Profit,
    ROUND(SUM(Revenue_generated) / SUM(Number_Of_Products_Sold), 2) AS 'Revenue per Product',
    SUM(Number_Of_Products_Sold) AS '# of Products Sold',
    SUM(Order_Quantities) AS '# of Orders'
FROM Data_Source
GROUP BY Product_Type
ORDER BY Total_Revenue DESC;
```
| Product_Type | Total_Revenue | Profit | Revenue - Costs | Revenue per Product | # of Products Sold | # of Orders |
| ------------ | ------------- | ------ | --------------- | ------------------- | ------------------ | ----------- |
| skincare | 241628.16 | 219398.84 | 22229.32 | 11.66 | 20731 | 2099 |
| haircare | 174455.39 | 157126.53 | 17328.86 | 12.82 | 13611 | 1480 |
| cosmetics | 161521.27 | 148154.87 | 13366.4 | 13.74 | 11757 | 1343 |


Profitability is key to business success. We identified the top 10 products that generate the highest profit.
```sql
SELECT SKU,
    Product_Type,
    Revenue_generated - Costs AS Profit
FROM Data_Source
ORDER BY Profit DESC
LIMIT 10;
```
| SKU | Product_Type | Profit |
| --- | ------------ | ------ |
| SKU2 | haircare | 9435.829711914062 |
| SKU38 | cosmetics | 9352.645477294922 |
| SKU88 | cosmetics | 9340.825942993164 |
| SKU67 | skincare | 9304.52604675293 |
| SKU51 | haircare | 9171.483520507812 |
| SKU31 | skincare | 9045.75555419922 |
| SKU99 | haircare | 8974.44253540039 |
| SKU52 | skincare | 8832.864196777344 |
| SKU32 | skincare | 8810.376892089844 |
| SKU18 | haircare | 8771.193542480469 |


We analyzed lead times by product type to identify which categories may be experiencing delays in the supply chain.
```sql
SELECT Product_type,
    AVG(Lead_Times) AS Average_Lead_Time,
    AVG(Stock_Levels) AS Average_Stock_Level,
    AVG(Availability) AS Average_Availability
FROM Data_Source
GROUP BY Product_type
ORDER BY 2 DESC;
```
| Product_type | Average_Lead_Time | Average_Stock_Level | Average_Availability |
| ------------ | ----------------- | ------------------- | -------------------- |
| skincare | 16.7000 | 40.2000 | 50.9250 |
| haircare | 15.5294 | 48.3529 | 43.2647 |
| cosmetics | 15.3846 | 58.6538 | 51.2308 |


We examined transportation modes and routes to identify inefficiencies in the supply chain, focusing on lead times and costs.
```sql
SELECT Transportation_Modes AS Transportation,
    Routes,
    ROUND(AVG(Lead_Times), 2) AS Average_Lead_Time,
    ROUND(AVG(Costs), 2) AS Average_Costs
FROM Data_Source
GROUP BY Transportation_Modes, Routes
ORDER BY 3 ASC, 1, 2;
```
| Transportation | Routes | Average_Lead_Time | Average_Costs |
| -------------- | ------ | ----------------- | ------------- |
| Sea | Route B | 9.83 | 564.34 |
| Sea | Route C | 10.50 | 341.78 |
| Rail | Route A | 12.43 | 485.05 |
| Rail | Route C | 14.00 | 456.94 |
| Sea | Route A | 15.14 | 335.68 |
| Air | Route A | 15.64 | 527.35 |
| Road | Route A | 16.36 | 539.49 |
| Road | Route C | 17.00 | 586.54 |
| Road | Route B | 17.85 | 552.39 |
| Rail | Route B | 18.27 | 637.04 |
| Air | Route C | 19.75 | 542.35 |
| Air | Route B | 20.71 | 637.84 |

Finally, we evaluated supplier performance based on inspection results to ensure quality and reliability in the supply chain.
```sql
SELECT Supplier_Name,
    SUM(Pass) AS Pass,
    SUM(Fail) AS Fail,
    SUM(Pending) AS Pending,
    SUM(Pass) + SUM(Fail) + SUM(Pending) AS Total_Inspections,
    ROUND(SUM(Pass) / (SUM(Pass) + SUM(Fail) + SUM(Pending)) * 100, 2) AS Pass_Percentage,
    ROUND(SUM(Fail) / (SUM(Pass) + SUM(Fail) + SUM(Pending)) * 100, 2) AS Fail_Percentage,
    ROUND(SUM(Pending) / (SUM(Pass) + SUM(Fail) + SUM(Pending)) * 100, 2) AS Pending_Percentage
FROM (
    SELECT Supplier_Name,
        Inspection_Results,
        CASE
            WHEN Inspection_Results = 'Pass' THEN 1
            ELSE 0
        END AS Pass,
        CASE
            WHEN Inspection_Results = 'Fail' THEN 1
            ELSE 0
        END AS Fail,
        CASE
            WHEN Inspection_Results = 'Pending' THEN 1
            ELSE 0
        END AS Pending
    FROM Data_Source
) AS subquery
GROUP BY Supplier_Name
ORDER BY Supplier_Name;
```
| Supplier_Name | Pass | Fail | Pending | Total_Inspections | Pass_Percentage | Fail_Percentage | Pending_Percentage |
| ------------- | ---- | ---- | ------- | ----------------- | --------------- | --------------- | ------------------ |
| Supplier 1 | 13 | 6 | 8 | 27 | 48.15 | 22.22 | 29.63 |
| Supplier 2 | 5 | 8 | 9 | 22 | 22.73 | 36.36 | 40.91 |
| Supplier 3 | 2 | 3 | 10 | 15 | 13.33 | 20.00 | 66.67 |
| Supplier 4 | 0 | 12 | 6 | 18 | 0.00 | 66.67 | 33.33 |
| Supplier 5 | 3 | 7 | 8 | 18 | 16.67 | 38.89 | 44.44 |

This analysis provides a holistic view of the company's operations, from product performance to supply chain efficiency. By addressing these questions, we can make data-driven decisions to improve overall business outcomes.