Модуль 9
1
```
CREATE VIEW shop.book_vip
AS (
	SELECT b.id book_id, b.name book_name, p.price_value
	FROM BookStore.books b
	JOIN BookStore.price p
		ON b.id = p.book_id
	JOIN BookStore.price_categories pc
		ON p.price_category_no = pc.price_category_no
	WHERE pc.price_category_name = 'Цена VIP клиента'
)
```

2
```
SELECT v_books.book_id, v_books.book_name, v_books.price_value
FROM shop.book_vip v_books
WHERE v_books.book_id = 6
```

3
```
SELECT book.book_id, book.book_name
FROM (
	SELECT b.id book_id, b.name book_name,
		p.price_value,
		CASE WHEN p.price_value > 500
			THEN 'high'
			ELSE 'normal'
		END price_type
	FROM BookStore.books b
	JOIN BookStore.price p
		ON b.id = p.book_id
) book
WHERE price_type = 'high'
```
4
```
SELECT client_login 
	, total_sum totalsalesamount
	, total_sum / total_orders avgsalesamount
FROM (
	SELECT om.client_login
		, count(*) total_orders
		, sum(NULLIF(od.qty, 0) * p.price_value) AS total_sum
	FROM shop.client c
	LEFT JOIN shop.order_main om
		ON c.client_login = om.client_login
	LEFT JOIN shop.order_detail od
		ON om.order_id = od.order_id
	JOIN BookStore.price p
		ON od.price_category_no = p.price_category_no
	GROUP BY om.client_login
) tmp;
```
5
```
WITH bookBeverages AS (
	SELECT om.client_login
		, count(*) total_orders
		, sum(NULLIF(od.qty, 0) * p.price_value) AS total_sum
	FROM shop.client c
	LEFT JOIN shop.order_main om
		ON c.client_login = om.client_login
	LEFT JOIN shop.order_detail od
		ON om.order_id = od.order_id
	JOIN BookStore.price p
		ON od.price_category_no = p.price_category_no
	GROUP BY om.client_login
)
SELECT client_login 
	, total_sum totalsalesamount
	, total_sum / total_orders avgsalesamount
FROM bookBeverages;
```
6
```
WITH c2020 AS (
	SELECT om.client_login
		, sum(NULLIF(od.qty, 0) * p.price_value) AS salesamt2020
	FROM shop.client c
	LEFT JOIN shop.order_main om
		ON c.client_login = om.client_login
	LEFT JOIN shop.order_detail od
		ON om.order_id = od.order_id
	JOIN BookStore.price p
		ON od.price_category_no = p.price_category_no
	WHERE EXTRACT (YEAR FROM om.order_date)::int = 2020
	GROUP BY om.client_login
)
SELECT c.client_login, c.lastname, salesamt2020
FROM c2020 
left join shop.client c
	on c.client_login=c2020.client_login;
```
7
```
WITH sales2019 AS (
	SELECT c.client_login
		, (
			SELECT sum(NULLIF(od.qty, 0) * p.price_value)
			FROM shop.client c2
			LEFT JOIN shop.order_main om
				ON c2.client_login = om.client_login
			LEFT JOIN shop.order_detail od
				ON om.order_id = od.order_id
			JOIN BookStore.price p
				ON od.price_category_no = p.price_category_no
			WHERE EXTRACT (YEAR FROM om.order_date)::int = 2019
				AND c2.client_login = c.client_login
		) amount
	FROM shop.client c
), sales2020 AS (
	SELECT c.client_login
		, (
			SELECT sum(NULLIF(od.qty, 0) * p.price_value)
			FROM shop.client c2
			LEFT JOIN shop.order_main om
				ON c2.client_login = om.client_login
			LEFT JOIN shop.order_detail od
				ON om.order_id = od.order_id
			JOIN BookStore.price p
				ON od.price_category_no = p.price_category_no
			WHERE EXTRACT (YEAR FROM om.order_date)::int = 2020
				AND c2.client_login = c.client_login
		) amount
	FROM shop.client c
)
SELECT client_login, lastname
	, sales2019.amount salesamt2019
	, sales2020.amount salesamt2020
	, NULLIF ((sales2020.amount - sales2019.amount) / sales2019.amount * 100, 0) pecent_up
FROM shop.client c 
LEFT JOIN sales2019 on c.client_login=c2029.client_login
LEFT JOIN sales2020 on c.client_login=c2020.client_login
ORDER BY pecent_up;
```
8
```
CREATE OR REPLACE VIEW shop.order_values AS (
	SELECT client_login
		, sum(NULLIF (od.qty, 0) * p.price_value) totalsalesamount
		, EXTRACT (YEAR FROM om.order_date)::int order_year
	FROM shop.order_main om
	JOIN shop.order_detail od USING (order_id)
	JOIN bookstore.price p USING (price_category_no)
	GROUP BY client_login, order_year
	ORDER BY client_login, order_year
);

DROP FUNCTION IF EXISTS shop.fnGetSalesByCustomer(orderyear int);

CREATE OR REPLACE FUNCTION shop.fnGetSalesByCustomer(orderyear int)
RETURNS TABLE (client_login varchar, totalsalesamount NUMERIC)
LANGUAGE plpgsql
AS
$$
	BEGIN
		RETURN QUERY
			SELECT ov.client_login, ov.totalsalesamount
			FROM shop.order_values ov
			WHERE ov.order_year = orderyear;
	END;
$$;
```
9
```
SELECT * FROM shop.fnGetSalesByCustomer(2019);
```
10
```
CREATE TABLE call_log
(
	call_id serial PRIMARY KEY,
	subscriber_id INTEGER NOT NULL,
	call_datetime TIMESTAMP WITH TIME ZONE
	DEFAULT statement_timestamp(),
	call_duration INTEGER NOT NULL
);

INSERT INTO call_log
	(subscriber_id, call_datetime, call_duration)
VALUES
	(1, '2020-01-01 03:11:03', 130),
	(2, '2020-01-01 07:01:11', 42),
	(3, '2020-01-02 13:10:01', 71),
	(4, '2020-01-02 22:17:15', 1083),
	(1, '2020-01-03 09:25:07', 22),
	(2, '2020-01-03 11:25:41', 823),
	(3, '2020-01-03 13:10:00', 235),
	(4, '2020-01-03 13:10:09', 14),
	(1, '2020-01-03 18:44:55', 201),
	(1, '2020-01-04 08:14:58', 111),
	(1, '2020-01-04 19:37:10', 342),
	(2, '2020-01-04 19:41:01', 61),
	(2, '2020-01-05 20:21:21', 38),
	(2, '2020-01-06 14:00:59', 101),
	(3, '2020-01-06 13:46:22', 654),
	(3, '2020-01-06 17:19:35', 257),
	(3, '2020-01-06 17:33:33', 32),
	(4, '2020-01-06 20:29:00', 705),
	(4, '2020-01-07 11:01:01', 41),
	(4, '2020-01-07 12:11:04', 123),
	(1, '2020-01-07 13:21:36', 434),
	(1, '2020-01-07 14:31:09', 18),
	(2, '2020-01-07 15:41:23', 45),
	(2, '2020-01-07 16:11:44', 455),
	(3, '2020-01-08 12:22:12', 122),
	(3, '2020-01-09 10:32:34', 67),
	(4, '2020-01-09 12:13:54', 33),
	(4, '2020-01-09 14:34:59', 514),
	(1, '2020-01-09 21:54:01', 13),
	(2, '2020-01-10 04:32:43', 1242),
	(3, '2020-01-10 10:12:42', 42),
	(4, '2020-01-10 11:34:46', 237),
	(1, '2020-01-10 11:11:11', 711),
	(2, '2020-01-10 11:45:22', 56),
	(3, '2020-01-11 07:54:33', 94),
	(4, '2020-01-11 07:32:20', 68),
	(1, '2020-01-11 07:45:10', 80),
	(2, '2020-01-11 07:11:30', 72),
	(3, '2020-01-11 07:01:11', 26),
	(4, '2020-01-11 08:02:22', 97);

	(subscriber_id, call_datetime, call_duration)
VALUES
	(1, '2020-01-01 03:11:03', 130),
	(2, '2020-01-01 07:01:11', 42),
	(3, '2020-01-02 13:10:01', 71),
	(4, '2020-01-02 22:17:15', 1083),
	(1, '2020-01-03 09:25:07', 22),
	(2, '2020-01-03 11:25:41', 823),
	(3, '2020-01-03 13:10:00', 235),
	(4, '2020-01-03 13:10:09', 14),
	(1, '2020-01-03 18:44:55', 201),
	(1, '2020-01-04 08:14:58', 111),
	(1, '2020-01-04 19:37:10', 342),
	(2, '2020-01-04 19:41:01', 61),
	(2, '2020-01-05 20:21:21', 38),
	(2, '2020-01-06 14:00:59', 101),
	(3, '2020-01-06 13:46:22', 654),
	(3, '2020-01-06 17:19:35', 257),
	(3, '2020-01-06 17:33:33', 32),
	(4, '2020-01-06 20:29:00', 705),
	(4, '2020-01-07 11:01:01', 41),
	(4, '2020-01-07 12:11:04', 123),
	(1, '2020-01-07 13:21:36', 434),
	(1, '2020-01-07 14:31:09', 18),
	(2, '2020-01-07 15:41:23', 45),
	(2, '2020-01-07 16:11:44', 455),
	(3, '2020-01-08 12:22:12', 122),
	(3, '2020-01-09 10:32:34', 67),
	(4, '2020-01-09 12:13:54', 33),
	(4, '2020-01-09 14:34:59', 514),
	(1, '2020-01-09 21:54:01', 13),
	(2, '2020-01-10 04:32:43', 1242),
	(3, '2020-01-10 10:12:42', 42),
	(4, '2020-01-10 11:34:46', 237),
	(1, '2020-01-10 11:11:11', 711),
	(2, '2020-01-10 11:45:22', 56),
	(3, '2020-01-11 07:54:33', 94),
	(4, '2020-01-11 07:32:20', 68),
	(1, '2020-01-11 07:45:10', 80),
	(2, '2020-01-11 07:11:30', 72),
	(3, '2020-01-11 07:01:11', 26),
	(4, '2020-01-11 08:02:22', 97);

DROP FUNCTION IF EXISTS shop.fnGetLastDateMaxCalls(_subscriber_id bigint);

CREATE OR REPLACE FUNCTION shop.fnGetLastDateMaxCalls(_subscriber_id bigint)
RETURNS TABLE (last_date date)
LANGUAGE plpgsql
AS
$body$
	BEGIN
		RETURN QUERY
			SELECT call_datetime::timestamp::date call_date
			FROM call_log cl
			GROUP BY cl.subscriber_id, call_date
				HAVING cl.subscriber_id = _subscriber_id
			ORDER BY count(*) DESC, call_date DESC
			FETCH FIRST 1 ROW ONLY;
	END;
$body$;

DROP FUNCTION IF EXISTS shop.fnGetLastDateMaxLengthCall(_subscriber_id bigint);

CREATE OR REPLACE FUNCTION shop.fnGetLastDateMaxLengthCall(_subscriber_id bigint)
RETURNS TABLE (last_date date)
LANGUAGE plpgsql
AS
$body$
	BEGIN
		RETURN QUERY
			SELECT call_datetime::timestamp::date call_date
			FROM call_log cl
			GROUP BY cl.subscriber_id, call_date, call_duration
				HAVING cl.subscriber_id = _subscriber_id
			ORDER BY max(call_duration) DESC, call_duration DESC
			FETCH FIRST 1 ROW ONLY;
	END;
$body$;

DROP FUNCTION IF EXISTS shop.fnGetMinIntervalBetweenCalls(_subscriber_id bigint);

CREATE OR REPLACE FUNCTION shop.fnGetMinIntervalBetweenCalls(_subscriber_id bigint)
RETURNS TABLE (last_date interval)
LANGUAGE plpgsql
AS
$body$
	BEGIN
		RETURN QUERY
		SELECT min(intervalBetweenCalls)
		FROM (
			SELECT cl.subscriber_id, cl.call_datetime + cl.call_duration * INTERVAL '1 seconds' - 
			(
			    SELECT max(cl1.call_datetime)
			    FROM call_log cl1
			    WHERE cl1.subscriber_id = cl.subscriber_id
					AND cl.call_datetime > cl1.call_datetime
			) AS intervalBetweenCalls
			FROM call_log cl
		) tmp
		GROUP BY subscriber_id
		HAVING subscriber_id = _subscriber_id;
	END;
$body$;

SELECT cl.subscriber_id
	, shop.fnGetLastDateMaxCalls(cl.subscriber_id) date_max_calls
	, shop.fnGetLastDateMaxLengthCall(cl.subscriber_id) date_max_length_call
	, shop.fnGetMinIntervalBetweenCalls(cl.subscriber_id) min_interval_between_calls
FROM call_log cl
GROUP BY cl.subscriber_id
ORDER BY cl.subscriber_id;
```
