-- insert practic.customer_dim

insert into practic.customer_dim(customer_id,customer_name,segment)
select o.customer_id,
		o.customer_name,
		o.segment
from public.orders o
group by o.customer_id,
		o.customer_name,
		o.segment
order by  o.customer_id,
		o.customer_name;
	
	
-- checking customer_dim
select *,
		count(*) over() as num
from practic.customer_dim;

-- update orders 05401

update public.orders
set postal_code = 05401
where postal_code is null;

-- insert practic.geography_dim

insert into practic.geography_dim(country,city,state,region,postal_code,full_name_manager)
select distinct o.country,
				o.city,
				o.state,
				o.region,
				o.postal_code,
				p.person
from public.orders o
join public.people p 
	on o.region = p.region 
group by o.country,
		o.city,
		o.state,
		o.region,
		o.postal_code,
		p.person;

-- checking geography_dim
select *,
		count(*) over() as num
from practic.geography_dim;


-- insert practic.product_dim

insert into practic.product_dim(category, subcategory, product_name, product_id)
select  o.category,
		o.subcategory,
		o.product_name,
		o.product_id
from public.orders o
group by o.category,
			o.subcategory,
			o.product_name,
			o.product_id;


-- checking product_dim
select *
		--,count(*) over() as num
from practic.product_dim;

-- insert practic.shipping_dim

insert into practic.shipping_dim (ship_mode)
select  o.ship_mode
from public.orders o
group by o.ship_mode;

-- checking shipping_dim
select *,
		count(*) over() as num
from practic.shipping_dim;

-- insert practic.order_date_dim
      
INSERT INTO practic.order_date_dim (orders_date, "year", quarter, "month", week, "day")
select o.order_date, 
		extract (YEAR FROM o.order_date), 
		extract (QUARTER FROM o.order_date),
		extract (MONTH FROM o.order_date),
		extract ( WEEK FROM o.order_date),
		extract (day FROM o.order_date) 
from stg.orders o 
group by o.order_date,
		extract (YEAR FROM o.order_date),
		extract (QUARTER FROM o.order_date),
		extract (MONTH FROM o.order_date),
		extract ( WEEK FROM o.order_date),
		extract (day FROM o.order_date) 
order by o.order_date

-- insert practic.ship_date_dim 

INSERT INTO practic.ship_date_dim (ships_date, "year", quarter, "month", week, "day")
select o.ship_date, 
		extract (YEAR FROM o.ship_date), 
		extract (QUARTER FROM o.ship_date),
		extract (MONTH FROM o.ship_date),
		extract ( WEEK FROM o.ship_date),
		extract (day FROM o.ship_date) 
from stg.orders o 
group by o.ship_date, 
		extract (YEAR FROM o.ship_date),
		extract (QUARTER FROM o.ship_date),	
		extract (MONTH FROM o.ship_date), 
		extract ( WEEK FROM o.ship_date),
		extract (day FROM o.ship_date) 
order by o.ship_date

--insert sales.fact

insert into practic.sales_fact(order_id,
							sales,
							quantity,
							discount,
							profit,
							returned,
							geo_dim_id,
							customer_dim_id,
							ship_dim_id,
							product_dim_id,
							ship_date,
							order_date)
select distinct o.order_id,
				o.sales,
				o.quantity,
				o.discount,
				o.profit,
				r.returned,
				gd.geo_dim_id,
				cd.customer_dim_id,
				sd.ship_dim_id,
				pd.product_dim_id,
				o.ship_date,
				o.order_date
from stg.orders o
-- join to "returns" 
left join stg."returns" r 
		on o.order_id = r.order_id
-- join customer_dim on customer_id 
inner join practic.customer_dim cd 
		on o.customer_id = cd.customer_id
-- join practic.geography_dim on state, city and postal code
inner join practic.geography_dim gd 
		on o.state = gd.state and o.city = gd.city and o.postal_code = gd.postal_code 
-- join shipping on ship_mode
inner join practic.shipping_dim sd 
		on o.ship_mode = sd.ship_mode 
-- join product_dim on product_id 
inner join practic.product_dim pd 
		on o.product_id = pd.product_id
-- join practic.order_date
inner join practic.order_date_dim odd 
		on o.order_date = odd.orders_date
-- join practic.ship_date_dim
inner join practic.ship_date_dim sdd 
		on o.ship_date = sdd.ships_date 
		
		
-- checking
-- poluchilos 9993 =) gde to odnu stroku poteryal =)

select *
from dw.sales_fact sf 
inner join dw.shipping_dim s 
		on sf.ship_dim_id = s.ship_dim_id 
inner join dw.geography_dim gd 
		on sf.geo_dim_id = gd.geo_dim_id 
inner join dw.product_dim p
		on sf.product_dim_id = p.product_dim_id 
inner join dw.customer_dim cd
		on sf.customer_dim_id = cd.customer_dim_id 
inner join dw.order_date_dim odd 
		on odd.orders_date =sf.order_date 
inner join dw.ship_date_dim sdd 
		on sdd.ships_date = sf.ship_date 
		
