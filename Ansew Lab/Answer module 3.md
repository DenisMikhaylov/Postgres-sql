Модуль 1 

  Задача 2
  ```
  Select * from book_store.book
  ```
  
  Задача 3

  ```
  SELECT 
    firstname
    ,lastname
    ,email
  FROM shop.client;
  ```

  Задача 4
  ```
  SELECT 
    book_id
  FROM book_store.book_author;
  ```
  ```
   SELECT distinct
    book_id
  FROM book_store.book_author; 
  ```

  Задача 5
  ```
  SELECT 
    c.firstname
    ,c.lastname
  FROM shop.client as c;
  ```
  Задача 6
  ```
  SELECT 
    firstname Name
    ,lastname LName
    ,email "E-mail"
  FROM shop.client;
  ```
  Задача 7
  ```
  SELECT
    price_category_no,
    case
	    when price_category_no = 1 then 'VIP'
	    when price_category_no = 2 then 'No VIP'
	  else 'Другое'
    end
  FROM shop.order_detail;
  ```
