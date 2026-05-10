# supply-chain-data-analysis-using-sql

<img width="1536" height="1024" alt="supply-chain-data-analysis-using-sql" src="https://github.com/rajkmlcodes/supply-chain-data-analysis-using-sql/blob/main/banner.png?raw=true" />

## Project Overview
End-to-end SQL analysis of supply chain operations data 
covering order fulfillment, inventory management, 
GRN & putaway tracking, and stock transfer operations.

## Business Problems Solved
- Identified dispatch pending bottlenecks across warehouses
- Tracked GRN & putaway TAT performance by warehouse
- Analyzed inventory health and flagged reorder requirements
- Monitored STO fulfillment gaps between source and destination warehouses
- Performed data quality checks across 15,800+ supply chain records

## Dataset
- 4 tables — Orders, Inventory, GRN Putaway, STO Orders
- 15,800+ rows of realistic supply chain operations data
- Intentionally includes real-world data quality issues

## Tools Used
- MySQL 8.0
- MySQL Workbench

## Key Analysis Areas
- Data Cleaning & Preparation
- Order Fulfillment Analysis
- Inventory Performance Tracking
- GRN & Putaway TAT Analysis
- STO Order Monitoring
- KPI & Performance Metrics

````sql
select count(*) from sc_orders;
select count(*) from sc_inventory;
select count(*) from sc_grn_putaway;
select count(*) from sc_sto_orders;
````
````sql
-- getting all orders
select * from sc_orders;
````
````sql
select
	order_id,    
    warehouse_id,    
    order_status,    
    qty_ordered,     
    qty_dispatched
from sc_orders;
````
### Fetching all orders from Bangalore warehouse
````sql
select
	order_id,
    warehouse_id,
    city,
    order_status
from sc_orders
where city='Bangalore';
````
### Fetch high quantity orders above 100 units
````sql
select
	order_id,
    warehouse_id,
    qty_ordered,
    order_status
from sc_orders
where qty_ordered > 100;
````

### Find orders where city data is missing
````sql
select
	order_id,
    warehouse_id,
    city,
    order_status
from sc_orders
where city is null;
````

### Fetch all pending orders
````sql
select
	order_id,
    warehouse_id,
    order_status,
    qty_ordered
from sc_orders
where order_status = 'pending';
````
### Fetch orders where quantity ordered is above 300
````sql
select
	order_id,
    warehouse_id,
    qty_ordered,
    order_status
from sc_orders
where qty_ordered > 300;
````

### Find orders with no transport vendor assigned
````sql
select
	order_id,
    warehouse_id,
    order_status,
    transport_vendor
from sc_orders
where transport_vendor is null;
````

### fetch all orders for mumbail location
````sql
select
	order_id,
    warehouse_id,
    order_status,
    city
from sc_orders
where city='Mumbai';
````

### fetch records where order status is pending
````sql
select count(*)
from sc_orders
where order_status='Pending';
````

### fetch all orders for mumbail location where dispatch or order status is pending
````sql
select
	order_id,
    warehouse_id,
    order_status,
    city
from sc_orders
where city='Mumbai' and order_status='Pending';
````

### cross-checking of pending status in different case:
````sql
select count(*)
from sc_orders
where order_status = 'Pending'; -- Normal
-- where order_status = 'PENDING'; -- Uppercase
-- where order_status = 'pending'; -- lowercase
````


### comparing warehouse names variations
````sql
select count(*)
from sc_orders
where warehouse_id = 'BLR_WH_01';  -- this is the correct naming convention of wh
-- where warehouse_id = 'BLR WH 01'; -- few whs ids without underscore(_)
```` 

### Fetch orders from Mumbai or Delhi
````sql
select
	 order_id,
     warehouse_id,
     city,
     order_status
from sc_orders
-- where city = 'Mumbai' or city = 'Delhi';  -- we can use this way to fetch data for specified cities
where city in ('Mumbai', 'Delhi'); -- but this is optimized way and its also easy to write
````

### Fetch pending orders from Bangalore or Delhi
````sql
select
	 order_id,
     warehouse_id,
     city,
     order_status,
     qty_ordered
from sc_orders
where city in ('Bangalore', 'Delhi')
and order_status = 'Pending'
and qty_ordered > 200;
````

### GRN
````sql
select
	grn_id,
    warehouse_id,
    qty_received,
    qty_putaway,
    grn_tat_hours,
    grn_status
from sc_grn_putaway
where grn_tat_hours between 5 and 24
order by grn_tat_hours desc;
````

### STO: Show me all STO orders that are still in 'Picking' status — sort them by qty_requested, highest first. I need to prioritize large pending picks.
````sql
select
	sto_id,
    source_warehouse,
    destination_warehouse,
    sku_id,
    qty_requested,
    qty_dispatched,
    qty_received,
    (qty_requested - qty_dispatched) as shipping_pending, -- checking how much qtys shipping is pending
	(qty_dispatched - qty_received) as receiving_pending, -- checking how much qtys receiving is pending
    sto_status
from sc_sto_orders
where sto_status = 'Picking'
order by qty_requested desc;
````

### Fetch picking STO orders with valid pending calculations only
````sql
select
	sto_id,
    source_warehouse,
    destination_warehouse,
    sku_id,
    qty_requested,
    qty_dispatched,
    qty_received,
    (qty_requested - qty_dispatched) as shipping_pending, -- checking how much qtys shipping is pending
	(qty_dispatched - qty_received) as receiving_pending, -- checking how much qtys receiving is pending
    sto_status
from sc_sto_orders
where sto_status = 'Picking'
and qty_dispatched <= qty_requested  -- fetching filtered data with no negative dispatch qtys
and qty_received <=  qty_dispatched -- fetching filtered data with no negative received qtys
order by qty_requested desc;
````

### Show all inventory records where closing stock is below reorder level — sort by warehouse first, then closing stock lowest first.    
````sql
select
	inv_id,
    warehouse_id,
    sku_id,
    category,
    opening_stock,
    stock_received,
    stock_sold,
    closing_stock,
    reorder_level
from sc_inventory
where closing_stock < reorder_level
and closing_stock >= 0
order by warehouse_id asc, closing_stock asc;
````

````sql
select count(*) from sc_inventory;
select count(*) from sc_inventory where closing_stock < reorder_level;
````

### Checking valid reorder records after removing negative stock
````sql
-- select count(*)
select
	inv_id,
    warehouse_id,
    sku_id,
    category,
    opening_stock,
    stock_received,
    stock_sold,
    closing_stock,
    reorder_level
from sc_inventory
where closing_stock < reorder_level
and closing_stock >= 0;
````    

### In the GRN table, find all unique GRN status values that contain the word 'Pend' anywhere in them
````sql
select distinct(grn_status)
from sc_grn_putaway
where grn_status like '%Pend%';
````

### Find all orders where SKU ID starts with 'SKU_01
````sql
select
	order_id,
    warehouse_id,
    qty_ordered,
    qty_dispatched,
    order_status
from sc_orders
where sku_id like 'SKU_01%';
````

## GROUPBY & AGGREGATIONS
### Your manager wants a summary report — how many orders does each city have? He wants to know which cities have the highest order volume.
````sql
select
    city,
    count(*) as total_orders
from sc_orders
group by city
order by total_orders desc;
````

### Manager says: I need warehouse-wise total quantity ordered — which warehouse is handling the most volume?
````sql
select
	warehouse_id,
    sum(qty_ordered) as total_qty_ordered
from sc_orders
group by warehouse_id
order by total_qty_ordered desc;
````

### Select Statement
````sql
select * from sc_inventory;
select * from sc_sto_orders;
select * from sc_grn_putaway;
select * from sc_orders;
````
