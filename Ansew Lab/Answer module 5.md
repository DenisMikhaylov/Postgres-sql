```
Select
 *
from shop.client c 
 where c.firstname = 'Иван';
```
```
Select
  *
from shop.client c 
  where c.firstname in ('Иван','Михаил');
```
```
Select
  *
from shop.client c 
  where c.firstname like 'А%'
```
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
```
Select
  c.client_login
  ,c.firstname
  ,o.order_id
FROM shop.client c
  left join shop.order_main om
    ON c.client_login = om.client_login
Where om.order_id is null '
```
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
```
Select
  *
from shop.order_main om 
Order by om.order_date desc
limit 20
```
```
Select
  *
from shop.order_main om 
Order by om.order_date desc
offset 20 limit 20
```
