Базы данных и SQL (семинары)
Урок 1. Установка СУБД, подключение к БД, просмотр и создание таблиц

 Задача 1
Создайте таблицу (сущность) с мобильными телефонами mobile_phones. При создании необходимо использовать DDL-команды.
Перечень полей (атрибутов):
id – числовой тип, автоинкремент, первичный ключ;
product_name – строковый тип, обязательный к заполнению;
manufacturer – строковый тип, обязательный к заполнению;
product_count – числовой тип, беззнаковый;
price – числовой тип, беззнаковый.
Используя CRUD-операцию INSERT, наполните сущность mobile_phones данными:


DROP TABLE IF EXISTS Название вашей схемы.mobile_phones;
CREATE TABLE Название вашей схемы.mobile_phones (
    id SERIAL PRIMARY KEY,
    product_name VARCHAR(255) NOT NULL,
    manufacturer VARCHAR(255) NOT NULL,
    product_count INT,
    price INT
);

 
INSERT INTO Название вашей схемы.mobile_phones (product_name, manufacturer, product_count, price) VALUES
('iPhone X', 'Apple', 156, 76000),
('iPhone 8', 'Apple', 180, 51000),
('Galaxy S9', 'Samsung', 21, 56000),
('Galaxy S8', 'Samsung', 124, 41000),
('P20 Pro', 'Huawei', 341, 36000);

 
SELECT * FROM Название вашей схемы.mobile_phones;



Задача 2

Имеется таблица (сущность) с мобильными телефонами mobile_phones.
У сущности имеются следующие поля(атрибуты):
id – идентификатор;
product_name – название;
manufacturer – производитель;
product_count – количество;
price – цена.
Необходимо вывести название, производителя и цену для мобильных телефонов, у которых количество больше чем 2.


SELECT product_name, manufacturer, price
FROM mobile_phones
WHERE product_count > 2;


Задача 3

Имеется таблица (сущность) с мобильными телефонами mobile_phones.
У сущности имеются следующие поля(атрибуты):
id – идентификатор;
product_name – название;
manufacturer – производитель;
product_count – количество;
price – цена.
Необходимо вывести идентификатор, название, производителя, количество и цену для мобильных телефонов, у которых производитель «Samsung».

SELECT id, product_name, manufacturer, product_count, price
FROM mobile_phones
WHERE manufacturer = 'Samsung';




Урок 2. SQL – создание объектов, простые запросы выборки

Задача 1

DROP TABLE IF EXISTS Название вашей схемы.mobile_phones;
CREATE TABLE iНазвание вашей схемы.mobile_phones (
    id SERIAL PRIMARY KEY,
    product_name VARCHAR(255) NOT NULL,
    manufacturer VARCHAR(255) NOT NULL,
    product_count INT,
    price INT
);

 
INSERT INTO Название вашей схемы.mobile_phones (product_name, manufacturer, product_count, price) 
VALUES
('iPhone X', 'Apple', 156, 76000),
('iPhone 8', 'Apple', 180, 51000),
('Galaxy S9', 'Samsung', 21, 56000),
('Galaxy S8', 'Samsung', 124, 41000),
('P20 Pro', 'Huawei', 341, 36000);

 
SELECT * FROM Название вашей схемы.mobile_phones;

CREATE TABLE Название вашей схемы.manufacturer (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100)
);

INSERT INTO Название вашей схемы.manufacturer (name)
VALUES 
    ('Apple'),
    ('Samsung'),
    ('Huawei');
 INSERT INTO Название вашей схемы.manufacturer (name)
SELECT DISTINCT manufacturer FROM Название вашей схемы.mobile_phones;


Задача 2

SELECT
    product_name,
    manufacturer,
    CASE
        WHEN product_count < 100 THEN 'little'
        WHEN product_count >= 100 AND product_count <= 300 THEN 'many'
        ELSE 'lots'
    END AS quantity_status
FROM
    mobile_phones;



Задача 3

ALTER TABLE Название вашей схемы.mobile_phones
ADD COLUMN manufacturer_id INT,
ADD CONSTRAINT fk_manufacturer_id
    FOREIGN KEY (manufacturer_id)
    REFERENCES Название вашей схемы.manufacturer(id)
    ON UPDATE CASCADE
    ON DELETE SET NULL;

UPDATE Название вашей схемы.mobile_phones AS m
SET manufacturer_id = (
    SELECT id
    FROM Название вашей схемы.manufacturer AS man
    WHERE man.name = m.manufacturer
);

ALTER TABLE Название вашей схемы.mobile_phones
DROP COLUMN manufacturer;

SELECT id, product_name, manufacturer_id
FROM Название вашей схемы.mobile_phones;


Задача 4

SELECT
    id,
    CASE
        WHEN order_status = 'OPEN' THEN 'Order is in open state'
        WHEN order_status = 'CLOSED' THEN 'Order is closed'
        WHEN order_status = 'CANCELLED' THEN 'Order is cancelled'
        ELSE 'Unknown status'
    END AS status_description
FROM
    orders;






Урок 3. SQL – выборка данных, сортировка, агрегатные функции



Задача 1

SELECT id, firstname, lastname, salary
FROM staff
ORDER BY salary ASC;


Задача 2

SELECT id, firstname, lastname, salary
FROM staff
ORDER BY salary DESC;


Задача 3

SELECT id, firstname, lastname, salary
FROM staff
ORDER BY salary DESC
LIMIT 5;


Задача 4

SELECT post, SUM(salary) AS total_salary
FROM staff
GROUP BY post;


Задача 5

SELECT COUNT(*) AS worker_count
FROM staff
WHERE post = 'Рабочий' AND age BETWEEN 24 AND 49;


Задача 6

SELECT COUNT(DISTINCT post) AS unique_posts_count
FROM staff;


Задача 7

SELECT post
FROM staff
GROUP BY post
HAVING AVG(age) <=30;





Урок 4. SQL – работа с несколькими таблицами

Задаяа 1

SELECT
  COUNT(*)
FROM users AS U
JOIN profiles AS P
  ON U.id = P.user_id
JOIN media AS M
  ON U.id = M.user_id
JOIN likes AS L
  ON M.id = L.media_id
WHERE
  P.gender = 'f';



Задача 2

SELECT
  CASE
    WHEN p.gender = 'f' THEN 'женский'
    WHEN p.gender = 'm' THEN 'мужской'
  END AS gender,
  COUNT(l.id) AS cnt    
FROM profiles AS p
JOIN likes AS l
  ON p.user_id = l.user_id
GROUP BY
  gender 
ORDER BY
  cnt     DESC;



Задача 3

SELECT
  U.id
FROM users AS U
LEFT JOIN
  messages AS M
ON
  U.id = M.from_user_id
WHERE
  M.from_user_id IS NULL;


Задача 4

SELECT
  u.id,
  u.firstname,
  u.lastname,
  COUNT(DISTINCT CASE
    WHEN f.status = 'approved' THEN
      CASE
        WHEN f.initiator_user_id = u.id THEN f.target_user_id
        WHEN f.target_user_id = u.id THEN f.initiator_user_id
      END
  END) AS friend_count
FROM users AS u
LEFT JOIN friend_requests AS f
  ON u.id IN (f.initiator_user_id, f.target_user_id)
GROUP BY
  u.id,
  u.firstname,
  u.lastname
ORDER BY
  u.id;







