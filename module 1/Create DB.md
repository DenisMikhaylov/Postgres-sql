Создание Базы данных

Первый вариант psql
Второй вариант PG Admin или DBever

Уделение базы данных, если она существует

```
DROP DATABASE IF EXISTS "BookShop";
```

Создание базы данных

```
CREATE DATABASE "BookShop" WITH OWNER = student
```
```
CREATE EXTENSION ltree SCHEMA public;
```
```
CREATE SCHEMA book_store AUTHORIZATION student;
```

Создание таблицы Жанр книг
```
CREATE TABLE book_store.genre
(
	genre_id	serial	PRIMARY KEY,
	parent		integer NOT NULL,
	genre_name	varchar(511) NOT NULL UNIQUE
);
```
