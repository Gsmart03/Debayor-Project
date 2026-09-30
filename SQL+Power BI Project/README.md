**use youtube_learn;
select count(*) from sales;


set sql_safe_updates = 0;

update sales
set purchase_date = str_to_date(purchase_date, '%d/%m/%Y');


alter table sales
modify purchase_date date;


update sales
set time_of_purchase = str_to_date(time_of_purchase, '%H:%i:%s');

alter table sales
modify time_of_purchase time;

UPDATE sales
SET gender = 'Female'
WHERE gender = 'F';


select * from sales;


-- What are the top 5 most selling products by quantity?

select product_name, sum(quantity) as total_quantity
from sales
where status = 'delivered'
group by product_name
order by total_quantity desc
limit 5; 


-- Which products are most frequently cancelled?

select product_name, count(*) as cancelled_products
from sales
where status = 'cancelled'
group by product_name
order by cancelled_products desc
limit 5;


-- What times of the day has the highest number of purchases?
select
	case
		when hour(time_of_purchase) between 6 and 11 then 'morning'
		when hour(time_of_purchase) between 12 and 17 then 'afternoon'
		when hour(time_of_purchase) between 18 and 23 then 'evening'
    else 'Night'
    end as day_time_record,
    count(*) as number_purchase
from sales
where status = 'delivered'
group by day_time_record
order by number_purchase desc;
    
    
    
-- Who are the top 5 highest spending customers?
select customer_id, customer_name, sum(quantity*price) as total_spend
from sales
where status = 'delivered'
group by customer_id, customer_name
order by total_spend desc
limit 5;





-- Which product categories generate the highest revenue?
select product_category, sum(quantity*price) as total_revenue
from sales
where status = 'delivered'
group by product_category
order by total_revenue desc;





-- What is the return/cancellation rate per product category?

select product_category,
	count(*) as total_order,
    sum(status = 'returned') as returned_orders,
	sum(status = 'cancelled') as cancelled_orders,
    round(sum(status = 'returned')/ count(*),2) as returned_rate,
	round(sum(status = 'cancelled') /count(*), 2) cancelled_rate
from sales
group by product_category;
      



-- What is the preferred payment mode?
select 
	payment_mode,
    count(*) as total_orders
    from sales
    group by payment_mode
    order by total_orders desc
    limit 1;
    
    
-- How does age group affect purchasing behaviour?
select 
	case
		when customer_age between 18 and 25 then '18-25'
        when customer_age between 26 and 35 then '26-35'
        when customer_age between 36 and 50 then '36-50'
        else '50+'
        end as age_group,
	sum(quantity * price) as total_amount
from sales
group by age_group
order by total_amount desc;
    
-- What is the monthly sales trend?
select
	date_format(purchase_date, '%Y-%m') as monthly_purchase,
    sum(quantity *price) as total_amount,
    sum(quantity)
    from sales
    group by monthly_purchase
    order by monthly_purchase;



-- Are certain genders buying more specific product categories?

select 
gender,
product_category,
count(product_category) as total_purchase
from sales
group by gender, product_category
order by total_purchase desc;**
