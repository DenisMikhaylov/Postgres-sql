
--<<Задача 1>>--
```
SELECT b.book_id, b.book_name
FROM book_store.book b
EXCEPT
SELECT b.book_id, b.book_name
FROM book_store.book b
LEFT JOIN book_store.price p ON b.book_id = p.book_id
WHERE p.price_category_no = 1

--<<Задача 2>>--

SELECT b.book_id, b.book_name
FROM book_store.book b
LEFT JOIN shop.order_detail od ON b.book_id = od.book_id
LEFT JOIN shop.order_main om ON od.order_id = om.order_id
LEFT JOIN book_store.price p ON od.book_id = p.book_id
GROUP BY b.book_id, b.book_name
HAVING SUM(od.qty * p.price_value) > 10000
```
--<<Задача 3>>--
```
SELECT book_id, book_name
FROM book_store.book
EXCEPT
SELECT b.book_id, b.book_name
FROM book_store.book b
LEFT JOIN book_store.price p ON b.book_id = p.book_id
WHERE p.price_category_no = 1

UNION

SELECT b.book_id, b.book_name
FROM book_store.book b
LEFT JOIN shop.order_detail od ON b.book_id = od.book_id
LEFT JOIN shop.order_main om ON od.order_id = om.order_id
LEFT JOIN book_store.price p ON od.book_id = p.book_id
GROUP BY b.book_id, b.book_name
HAVING SUM(od.qty * p.price_value) > 10000


SELECT COUNT(*) AS total_rows
FROM (SELECT book_id, book_name
FROM book_store.book
EXCEPT SELECT b.book_id, b.book_name
FROM book_store.book b
LEFT JOIN book_store.price p ON b.book_id = p.book_id
WHERE p.price_category_no = 1
    
    UNION ALL
    
SELECT b.book_id, b.book_name
FROM book_store.book b
LEFT JOIN shop.order_detail od ON b.book_id = od.book_id
LEFT JOIN shop.order_main om ON od.order_id = om.order_id
LEFT JOIN book_store.price p ON od.book_id = p.book_id
GROUP BY b.book_id, b.book_name
HAVING SUM(od.qty * p.price_value) > 10000) AS combined_data
```
--<<Задача 4>>--
```
select * from (
select
 c.client_login ,
 c.firstname 
from
 shop.order_main om
join shop.order_detail od
on
 od.order_id = om.order_id
join book_store.price p on
 p.book_id = od.book_id
join shop.client c on
 c.client_login = om.client_login
where date_part ('year', om.order_date) = 2019
 group by
 c.client_login
order  by sum(od.qty * p.price_value) desc 
limit 10) as d1
union 
select * from (select
 c.client_login ,
 c.firstname 
from
 shop.order_main om
join shop.order_detail od
on
 od.order_id = om.order_id
join book_store.price p on
 p.book_id = od.book_id
join shop.client c on
 c.client_login = om.client_login
where date_part ('year', om.order_date) = 2020
 group by
 c.client_login
order  by sum(od.qty * p.price_value) desc 
limit 10) as d2

```
--<<Задача 5>>--
```
SELECT DISTINCT o.client_login
FROM shop.order_main o
LEFT JOIN (SELECT order_id, COUNT(DISTINCT book_id) AS num_products
    FROM shop.order_detail
    GROUP BY order_id
    HAVING COUNT(DISTINCT book_id) > 5) od ON o.order_id = od.order_id
```
--<<Задача 6>>--
```
SELECT DISTINCT client_login
FROM shop.order_main
EXCEPT
SELECT DISTINCT o.client_login
FROM shop.order_main o
LEFT JOIN (SELECT order_id, COUNT(DISTINCT book_id) AS num_products
    FROM shop.order_detail
    GROUP BY order_id
    HAVING COUNT(DISTINCT book_id) > 5) od ON o.order_id = od.order_id
```
--<<Задача 7>>--
```
SELECT o.client_login
FROM shop.order_main o
LEFT JOIN (SELECT od.order_id, SUM(od.qty * p.price_value) AS total_sales
    FROM shop.order_detail od
	LEFT JOIN book_store.price p ON od.book_id = p.book_id
    GROUP BY order_id) od ON o.order_id = od.order_id
WHERE od.total_sales > 10000
```
--<<Задача 8>>--
```
SELECT DISTINCT client_login
FROM shop.order_main
EXCEPT
SELECT DISTINCT o.client_login
FROM shop.order_main o
LEFT JOIN (SELECT order_id, COUNT(DISTINCT book_id) AS num_products
    FROM shop.order_detail
    GROUP BY order_id
    HAVING COUNT(DISTINCT book_id) > 5) od ON o.order_id = od.order_id

INTERSECT

SELECT o.client_login
FROM shop.order_main o
LEFT JOIN (SELECT od.order_id, SUM(od.qty * p.price_value) AS total_sales
    FROM shop.order_detail od
	LEFT JOIN book_store.price p ON od.book_id = p.book_id
    GROUP BY order_id) od ON o.order_id = od.order_id
WHERE od.total_sales > 10000
```
