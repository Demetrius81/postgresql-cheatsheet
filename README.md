# 📚 Полная шпаргалка по PostgreSQL

## 1. Подключение

```bash
psql -h localhost -U user -d database
```

---

## 2. Основные команды psql

```sql
\l       -- список баз данных
\c mydb  -- подключиться к базе
\dt      -- список таблиц
\d table -- структура таблицы
\q       -- выход
```

---

## 3. Создание и удаление базы

```sql
CREATE DATABASE mydb;
DROP DATABASE mydb;
```

---

## 4. Создание таблицы

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    age INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## 5. Вставка данных

```sql
INSERT INTO users (name, age) VALUES ('Alice', 25);
INSERT INTO users (name, age) VALUES 
  ('Bob', 30),
  ('Charlie', 28);
```

---

## 6. Обновление данных

```sql
UPDATE users
SET age = 26
WHERE name = 'Alice';
```

---

## 7. Удаление данных

```sql
DELETE FROM users WHERE id = 1;
```

---

## 8. Выборка данных

```sql
SELECT * FROM users;
SELECT name, age FROM users WHERE age > 25;
SELECT name FROM users ORDER BY age DESC LIMIT 5 OFFSET 10;
```

---

## 9. Фильтры

```sql
SELECT * FROM users WHERE age BETWEEN 20 AND 30;
SELECT * FROM users WHERE name LIKE 'A%';
SELECT * FROM users WHERE age IN (25, 30, 35);
```

---

## 10. Агрегации

```sql
SELECT COUNT(*) FROM users;
SELECT AVG(age) FROM users;
SELECT MIN(age), MAX(age) FROM users;
SELECT age, COUNT(*) FROM users GROUP BY age HAVING COUNT(*) > 1;
```

---

## 11. JOIN'ы

```sql
-- INNER JOIN
SELECT u.name, o.amount
FROM users u
JOIN orders o ON u.id = o.user_id;

-- LEFT JOIN
SELECT u.name, o.amount
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;

-- RIGHT JOIN
SELECT u.name, o.amount
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;

-- FULL JOIN
SELECT u.name, o.amount
FROM users u
FULL JOIN orders o ON u.id = o.user_id;
```

---

## 12. Подзапросы

```sql
SELECT name
FROM users
WHERE age > (SELECT AVG(age) FROM users);
```

---

## 13. Индексы

```sql
CREATE INDEX idx_users_name ON users(name);
DROP INDEX idx_users_name;
```

---

## 14. Транзакции

```sql
BEGIN;
UPDATE users SET age = age + 1 WHERE id = 1;
COMMIT;

-- откат
ROLLBACK;
```

---

## 15. Пользователи и права

```sql
CREATE USER test WITH PASSWORD '1234';
GRANT ALL PRIVILEGES ON DATABASE mydb TO test;
```

---

## 16. Полезные функции

```sql
SELECT NOW();
SELECT CURRENT_DATE;
SELECT EXTRACT(YEAR FROM NOW());
```

---

## 17. CTE (Common Table Expressions)

```sql
WITH avg_age AS (
    SELECT AVG(age) AS avg_age FROM users
)
SELECT name, age FROM users, avg_age
WHERE users.age > avg_age.avg_age;
```

---

## 18. Рекурсивные CTE

```sql
WITH RECURSIVE subordinates AS (
    SELECT id, name, manager_id FROM employees WHERE id = 1
    UNION
    SELECT e.id, e.name, e.manager_id
    FROM employees e
    JOIN subordinates s ON s.id = e.manager_id
)
SELECT * FROM subordinates;
```

---

## 19. Оконные функции

```sql
SELECT name, age,
       AVG(age) OVER () AS avg_age,
       RANK() OVER (ORDER BY age DESC) AS rank_by_age
FROM users;
```

---

## 20. PARTITION BY

```sql
SELECT department, name, salary,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rank_in_dept
FROM employees;
```

---

## 21. JSON

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    data JSONB
);

INSERT INTO products (data) VALUES
('{"name": "Laptop", "price": 1000, "tags": ["tech", "sale"]}');

SELECT data->>'name' AS name FROM products;
SELECT * FROM products WHERE data->>'name' = 'Laptop';
```

---

## 22. Агрегация в JSON

```sql
SELECT json_agg(name) FROM users;
SELECT json_build_object('name', name, 'age', age) FROM users;
```

---

## 23. Массивы

```sql
SELECT ARRAY[1,2,3] @> ARRAY[2];
SELECT ARRAY[1,2,3] && ARRAY[3,4];
SELECT unnest(ARRAY[1,2,3]) AS element;
```

---

## 24. CASE

```sql
SELECT name,
       CASE
           WHEN age < 18 THEN 'Child'
           WHEN age BETWEEN 18 AND 64 THEN 'Adult'
           ELSE 'Senior'
       END AS category
FROM users;
```

---

## 25. DISTINCT ON

```sql
SELECT DISTINCT ON (department) department, name, salary
FROM employees
ORDER BY department, salary DESC;
```

---

## 26. Генерация данных

```sql
SELECT generate_series(1, 5);
SELECT generate_series('2023-01-01'::date, '2023-01-10', '1 day');
```

---

## 27. Интервалы

```sql
SELECT NOW() + INTERVAL '1 day';
SELECT AGE('2025-01-01', '2000-01-01');
```

---

## 28. Материализованные представления

```sql
CREATE MATERIALIZED VIEW top_users AS
SELECT name, age FROM users ORDER BY age DESC LIMIT 10;

REFRESH MATERIALIZED VIEW top_users;
```

---

## 29. Фильтры в агрегатах

```sql
SELECT COUNT(*) FILTER (WHERE age > 30) AS older_users
FROM users;
```

---

## 30. Full Text Search

```sql
SELECT * FROM articles
WHERE to_tsvector('english', content) @@ to_tsquery('rust & programming');
```

---

## 31. UUID

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE TABLE users_uuid (
    id UUID DEFAULT uuid_generate_v4(),
    name TEXT
);
```

---

## 32. Оптимизация и индексы

### EXPLAIN

```sql
EXPLAIN SELECT * FROM users WHERE name = 'Alice';
EXPLAIN ANALYZE SELECT * FROM users WHERE age > 30;
```

### Типы индексов

```sql
-- B-Tree (по умолчанию, для точного поиска и диапазонов)
CREATE INDEX idx_users_age ON users(age);

-- GIN (для JSON, full-text, массивов)
CREATE INDEX idx_products_data ON products USING gin (data);

-- GIST (геоданные, полнотекст)
CREATE INDEX idx_geo ON locations USING gist (geom);

-- BRIN (для больших последовательных данных)
CREATE INDEX idx_large_table ON huge_table USING brin (timestamp);
```

### VACUUM и ANALYZE

```sql
VACUUM ANALYZE; -- очистка и обновление статистики
```
