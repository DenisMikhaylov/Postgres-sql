--<<Задача 1>>-- 

SELECT om.order_id, om.order_date, p.price_value,
	ROW_NUMBER() OVER(ORDER BY om.order_date) AS rowno
FROM shop.order_main om
LEFT JOIN shop.order_detail od ON om.order_id = od.order_id
LEFT JOIN book_store.price p ON od.book_id = p.book_id
ORDER BY om.order_date

--<<Задача 2>>--

SELECT om.order_id, om.order_date, p.price_value,
	ROW_NUMBER() OVER(ORDER BY om.order_date) AS rowno,
    RANK() OVER(ORDER BY om.order_date) AS rankno
FROM shop.order_main om
LEFT JOIN shop.order_detail od ON om.order_id = od.order_id
LEFT JOIN book_store.price p ON od.book_id = p.book_id
ORDER BY om.order_date

--<<Задача 3>>--

SELECT om.order_id, om.order_date, om.client_login, p.price_value,
    RANK() OVER(PARTITION BY om.client_login ORDER BY p.price_value DESC) AS orderrankno
FROM shop.order_main om
LEFT JOIN shop.order_detail od ON om.order_id = od.order_id
LEFT JOIN book_store.price p ON od.book_id = p.book_id

--<<Задача 4>>--

SELECT om.client_login, p.price_value
    EXTRACT(YEAR FROM om.order_date) AS orderyear,
    RANK() OVER(PARTITION BY om.client_login, EXTRACT (YEAR FROM om.order_date) ORDER BY p.price_value DESC) AS orderrankno
FROM shop.order_main om
LEFT JOIN shop.order_detail od ON om.order_id = od.order_id
LEFT JOIN book_store.price p ON od.book_id = p.book_id

--<<Задача 5>>--

WITH OrderRows AS (SELECT om.order_id, om.order_date, p.price_value,
	ROW_NUMBER() OVER(ORDER BY om.order_date) AS rowno
FROM shop.order_main om
LEFT JOIN shop.order_detail od ON om.order_id = od.order_id
LEFT JOIN book_store.price p ON od.book_id = p.book_id
ORDER BY om.order_date)
SELECT curr.order_id, curr.order_date, curr.price_value, prev.price_value AS prevval, curr.price_value - prev.price_value AS diffprev
FROM OrderRows curr
LEFT JOIN OrderRows prev ON curr.rowno = prev.rowno + 1

--<<Задача 6>>--

SELECT curr.order_id, curr.order_date, curr.price_value,
   LAG(curr.price_value) OVER(ORDER BY curr.order_date) AS prevval, curr.price_value - LAG(curr.price_value) OVER(ORDER BY curr.order_date) AS diffprev
FROM (SELECT om.order_id, om.order_date, p.price_value, ROW_NUMBER() OVER(ORDER BY om.order_date) AS rowno
   FROM shop.order_main om
LEFT JOIN shop.order_detail od ON om.order_id = od.order_id
LEFT JOIN book_store.price p ON od.book_id = p.book_id) AS curr

