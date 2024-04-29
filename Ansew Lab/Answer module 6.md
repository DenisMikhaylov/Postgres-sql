Модуль 6
Задание 1
```
Select
  om.client_login
  ,om.order_date
from shop.order_main om
where order_date >= '20200201'
          and  order_date < '20200301'
```
```
where date_part('year', order_date) = 2020
          and  date_part('month', order_date) = 2
```

```
where order_date between '20200201' and '20200301'
```

```
Select now()
Select
  date_trunc('month', now())
  ,(date_trunc('month', now()) + interval '1 month - 1 day');

```

```
Select
  om.client_login
  ,om.order_date
  ,(date_trunc('month', om.order_date) + interval '1 month - 1 day')
  ,date_part('day' ,(date_trunc('month', om.order_date) + interval '1 month - 1 day')-om.order_date)
from shop.order_main om
where date_part('day' ,(date_trunc('month', om.order_date) + interval '1 month - 1 day')-om.order_date) <=5

```
```
Select distinct
  od.book_id 
from shop.order_main om
	left join shop.order_detail od 
		on od.order_id =om.order_id 
where extract (week from om.order_date) <=10
		and extract (year  from om.order_date) <=2020;
```
```
select 
	c.firstname 
	,c.lastname 
	,c.phone 
	,concat(c.firstname ,', ',c.lastname,'(phone: ',c.phone,')') 
from shop.client c  
```
```
Select
  length(lastname) - length(REPLACE(lastname, 'а', ''))
from shop.client

```
