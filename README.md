# Домашнее задание к занятию "`Работа с данными (DDL/DML)`" - `Юрочкин В.А.`

---

### Задание 1

1.1. Поднимите чистый инстанс MySQL версии 8.0+. Можно использовать локальный сервер или контейнер Docker.
1.2. Создайте учётную запись sys_temp.
1.3. Выполните запрос на получение списка пользователей в базе данных. (скриншот)
1.4. Дайте все права для пользователя sys_temp.
1.5. Выполните запрос на получение списка прав для пользователя sys_temp. (скриншот)
1.6. Переподключитесь к базе данных от имени sys_temp.
Для смены типа аутентификации с sha2 используйте запрос:
ALTER USER 'sys_test'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
1.6. По ссылке https://downloads.mysql.com/docs/sakila-db.zip скачайте дамп базы данных.
1.7. Восстановите дамп в базу данных.
1.8. При работе в IDE сформируйте ER-диаграмму получившейся базы данных. При работе в командной строке используйте команду для получения всех таблиц базы данных. (скриншот)
Результатом работы должны быть скриншоты обозначенных заданий, а также простыня со всеми запросами.

`Решение:`

1. Ставим MySQL 8.0+ локально 

```
sudo apt update
sudo apt install -y mysql-server
mysql --version
sudo systemctl enable --now mysql
sudo systemctl status mysql --no-pager
```

<img width="1099" height="961" alt="image" src="https://github.com/user-attachments/assets/3838e7f4-b2e9-4dee-9941-6a48dd93616c" />

2. Создаю учётную запись sys_temp

```
sudo mysql
CREATE USER 'sys_temp'@'localhost' IDENTIFIED BY 'Password123!';
```
Запрос: список пользователей

```
SELECT user, host, plugin
FROM mysql.user
ORDER BY user, host;
```

<img width="972" height="802" alt="image" src="https://github.com/user-attachments/assets/2dfef05c-0844-4788-bedd-169d80103cfa" />

3. Даю все права пользователю sys_temp

```
GRANT ALL PRIVILEGES ON *.* TO 'sys_temp'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

Запрос: список прав sys_temp

```
SHOW GRANTS FOR 'sys_temp'@'localhost';
```

выход:

```
EXIT;
```

4. Переподключаюсь как sys_temp

```
mysql -u sys_temp -p
```

Проверка, что я под этим пользователем:

```
SELECT USER(), CURRENT_USER();
```

<img width="957" height="582" alt="image" src="https://github.com/user-attachments/assets/e5039175-80e2-4c98-8ddf-0dfa56241ebd" />

выход:

```
EXIT;
```

5. скачиваю дамп

```
wget https://downloads.mysql.com/docs/sakila-db.zip
```

<img width="1501" height="385" alt="image" src="https://github.com/user-attachments/assets/082063ae-7dae-479c-8fe0-71185e6b6d61" />


6. распаковываю

```
unzip sakila-db.zip
```

<img width="719" height="350" alt="image" src="https://github.com/user-attachments/assets/f8681059-3667-4e37-8666-2a68b034e877" />


7. Разворачиваю дамп

```
sudo mysql < sakila-schema.sql
sudo mysql < sakila-data.sql
```

заходим и смотри что развернулось...

```
sudo mysql
USE sakila;
SHOW TABLES;
```

<img width="811" height="840" alt="image" src="https://github.com/user-attachments/assets/38904964-9436-4efc-ad07-c78e61792604" />

```
SELECT COUNT(*) AS actors FROM actor;
SELECT COUNT(*) AS films  FROM film;
```

<img width="571" height="421" alt="image" src="https://github.com/user-attachments/assets/ac0e174a-f9b6-4806-b2d1-310271afb20c" />


---


### Задание 2

Составьте таблицу, используя любой текстовый редактор или Excel, в которой должно быть два столбца: в первом должны быть названия таблиц восстановленной базы, во втором названия первичных ключей этих таблиц. Пример: (скриншот/текст) 

Название таблицы | Название первичного ключа
customer         | customer_id

`Решение:`

Сделаю проще, одним запросом из базы в котором будет отражена вся структура по заданию:

```
USE sakila;

SELECT
  t.TABLE_NAME,
  GROUP_CONCAT(k.COLUMN_NAME ORDER BY k.ORDINAL_POSITION) AS primary_key
FROM information_schema.TABLES t
JOIN information_schema.KEY_COLUMN_USAGE k
  ON t.TABLE_SCHEMA = k.TABLE_SCHEMA
 AND t.TABLE_NAME   = k.TABLE_NAME
 AND k.CONSTRAINT_NAME = 'PRIMARY'
WHERE t.TABLE_SCHEMA = 'sakila'
  AND t.TABLE_TYPE = 'BASE TABLE'
GROUP BY t.TABLE_NAME
ORDER BY t.TABLE_NAME;
```

<img width="965" height="896" alt="image" src="https://github.com/user-attachments/assets/d18b9492-1e36-40b0-a6ef-cb653b1dbdc9" />


---
