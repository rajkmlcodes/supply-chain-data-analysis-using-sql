# supply-chain-data-analysis-using-sql

<img width="1536" height="1024" alt="supply-chain-data-analysis-using-sql" src="" />

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


    


### Select Statement
````sql
select * from sc_inventory;
select * from sc_sto_orders;
select * from sc_grn_putaway;
select * from sc_orders;
````
