Модуль 07

Задача 1

```
select 
om.client_login
, c.firstname
,
from shop.order_main om 
	join shop.client c 
		on c.client_login =om.client_login 
	left join shop.order_detail od 
		on od.order_id = om.order_id 
where 	od.book_id =6
group by om.client_login, c.firstname
```
Задача 2

```
select 
om.client_login
, c.firstname
, c.phone 
from shop.order_main om 
	join shop.client c 
		on c.client_login =om.client_login 
	left join shop.order_detail od 
		on od.order_id = om.order_id 
where 	od.book_id =6
group by om.client_login, c.firstname , c.phone 
```

Задача 3
```
select 
om.client_login
, date_part ('year', om.order_date) as year
from shop.order_main om 
left join shop.order_detail od 
	on od.order_id = om.order_id
left join book_store.price_category pc 
	on pc.price_category_no = od.price_category_no
where pc.category_name ilike '%VIP%'
group by om.client_login, date_part ('year', om.order_date)
order by om.client_login, year;

```
Задача 5
```
select 
om.order_no 
, sum (od.qty*p.price_value)  salesamount
from shop.order_main om 
	left join shop.order_detail od 
		on od.order_id = om.order_id
	left join book_store.price p 
		on p.book_id = od.book_id and od.price_category_no = p.price_category_no  
group by om.order_no 
	order by salesamount desc;
```
