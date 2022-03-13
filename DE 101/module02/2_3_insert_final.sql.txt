-- insert dw.customer_dim

insert into dw.customer_dim(customer_id,customer_name,segment)
select o.customer_id,
		o.customer_name,
		o.segment
from stg.orders o
group by o.customer_id,
		o.customer_name,
		o.segment
order by  o.customer_id,
		o.customer_name;
	
	
-- checking customer_dim
select *,
		count(*) over() as num
from dw.customer_dim;


-- insert dw.geography_dim

insert into dw.geography_dim(country,city,state,region,postal_code,full_name_manager)
select distinct o.country,
				o.city,
				o.state,
				o.region,
				o.postal_code,
				p.person
from stg.orders o
join stg.people p 
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
from dw.geography_dim;


-- insert dw.product_dim

insert into dw.product_dim(category, subcategory, product_name, product_id)
select  o.category,
		o.subcategory,
		o.product_name,
		o.product_id
from stg.orders o
group by o.category,
			o.subcategory,
			o.product_name,
			o.product_id;


-- checking product_dim
select *,
		count(*) over() as num
from dw.product_dim;

-- insert dw.shipping_dim

insert into dw.shipping_dim (ship_mode)
select  o.ship_mode
from stg.orders o
group by o.ship_mode;

-- checking shipping_dim
select *,
		count(*) over() as num
from dw.shipping_dim;

-- insert calendar_dim

insert into dw.calendar_dim(date_dim_id,year,quarter,"month",week,"date",week_day,leap)
select 
to_char(date,'yyyymmdd')::int as date_dim_id, 
       extract('year' from date)::int as year,
       extract('quarter' from date)::int as quarter,
       extract('month' from date)::int as month,
       extract('week' from date)::int as week,
       date::date,
       to_char(date, 'dy') as week_day,
       case when (extract('year' from date)::int % 4 = 0) and 
       			(extract('year' from date)::int % 100 != 0) or 	
       			(extract('year' from date)::int % 400 = 0) 
       		then True
       		else False
       end
       as leap 
  from generate_series(date '2015-01-01',
                       date '2030-01-01',
                       interval '1 day')
       as t(date);
      
      
--checking
      
select *
from dw.calendar_dim; 


-- insert dw.sales_fact
		
insert into dw.sales_fact(order_id,
							sales,
							quantity,
							discount,
							profit,
							returned,
							geo_dim_id,
							customer_dim_id,
							ship_dim_id,
							product_dim_id,
							ship_date_id,
							order_date_id)
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
				to_char(o.ship_date, 'yyyymmdd')::int as ship_date_id,
				to_char(o.order_date, 'yyyymmdd')::int as order_date_id
from stg.orders o
-- join to "returns" 
left join stg."returns" r 
		on o.order_id = r.order_id
-- join customer_dim on customer_id 
inner join dw.customer_dim cd 
		on o.customer_id = cd.customer_id
-- join practic.geography_dim on state, city and postal code
inner join dw.geography_dim gd 
		on o.state = gd.state and o.city = gd.city and o.postal_code = gd.postal_code 
-- join shipping on ship_mode
inner join dw.shipping_dim sd 
		on o.ship_mode = sd.ship_mode 
-- join product_dim on product_id 
inner join dw.product_dim pd 
		on o.product_id = pd.product_id 
		
		
		
		
-- checking
-- poluchilos 9993 =) gde to odnu stroku poteryal =)

select count(*) from dw.sales_fact sf
inner join dw.shipping_dim s on sf.ship_dim_id = s.ship_dim_id 
inner join dw.geography_dim gd on sf.geo_dim_id = gd.geo_dim_id 
inner join dw.product_dim p on sf.product_dim_id = p.product_dim_id 
inner join dw.customer_dim cd on sf.customer_dim_id = cd.customer_dim_id ;


