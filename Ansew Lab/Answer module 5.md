Модуль 5

Задача 1
```
Select
 *
from shop.client c 
 where c.firstname = 'Иван';
```

Задача 2
```
Select
  *
from shop.client c 
  where c.firstname in ('Иван','Михаил');
```
Задача 3
```
Select
  *
from shop.client c 
  where c.firstname like 'А%'
```
Задача 4
```
Select
  c.client_login
  ,c.firstname
  ,o.order_id
FROM shop.client c
  left join shop.order_main om
    ON c.client_login = om.client_login
Where c.firstname = 'Иван'
```
Задача 5
```
Select
  c.client_login
  ,c.firstname
  ,o.order_id
FROM shop.client c
  left join shop.order_main om
    ON c.client_login = om.client_login
Where om.order_id is null
```
Задача 6
```
Select
  c.client_login
  ,c.firstname
  ,o.order_id
FROM shop.client c
  left join shop.order_main om
    ON c.client_login = om.client_login
Where om.order_date >='20191029'
Order by om.order_date, om.client_login desc
```

Задача 7
```
Select
  *
from shop.order_main om 
Order by om.order_date desc
limit 20
```
Задача 8
```
Select
  *
from shop.order_main om 
Order by om.order_date desc
offset 20 limit 20
```
