```
Select
  b.book_name
  ,p.price_category_no
from book_store.book b 
	left join book_store.price p 
		on b.book_id = p.book_id 
```


```
select book_store.book.book_id 
, book_store.book_author.author_id
from book_store.book
	inner join book_store.book_author 
		on book_store.book_author.book_id = book_store.book.book_id
```

```
Select
  b.book_id 
  ,ba.author_id
from book_store.book b
	left join book_store.book_author ba
		on ba.book_id = b.book_id
```

```
Select
  b.book_id 
  ,ba.author_id
  ,a.author_name
  ,a.biography
from book_store.book b
	left join book_store.book_author ba
		on b.book_id = ba.book_id
	left join book_store.author a 
		on ba.author_id = a.author_id 

```
```
Select
  genre_id
  ,parent
  ,genre_name
FROM book_store.genre;
```
```
Select
  h.genre_id
  ,h.parent
  ,h.genre_name
  ,g.genre_name 
FROM book_store.genre as g
	join book_store.genre as h 
		on g.genre_id = h.genre_id
```
```
Select
  c.firstname
  ,c.lastname 
  ,m.order_no  
FROM shop.client as c
	left join shop.order_main as m 
		on c.client_login = m.client_login 
```
