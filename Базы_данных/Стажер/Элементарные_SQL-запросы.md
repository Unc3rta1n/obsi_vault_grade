# Элементарные SQL-запросы

SQL (Structured Query Language) — это язык структурированных запросов, используемый для работы с реляционными базами данных. В этом разделе рассмотрим базовые SQL-запросы, необходимые для работы с данными.

## 1. Выборка данных (`SELECT`)

Оператор `SELECT` используется для получения данных из таблицы.

### Получить все данные из таблицы:
```sql
SELECT * FROM users;
```

### Получить конкретные столбцы:
```sql
SELECT first_name, last_name FROM users;
```

### Фильтрация данных с `WHERE`:
```sql
SELECT * FROM users WHERE age > 18;
```

### Использование `ORDER BY` для сортировки:
```sql
SELECT * FROM users ORDER BY last_name ASC;
```

## 2. Вставка данных (`INSERT`)

Оператор `INSERT INTO` добавляет новые записи в таблицу.

```sql
INSERT INTO users (first_name, last_name, age) 
VALUES ('Иван', 'Иванов', 25);
```

## 3. Обновление данных (`UPDATE`)

Оператор `UPDATE` изменяет существующие записи.

```sql
UPDATE users 
SET age = 26 
WHERE first_name = 'Иван' AND last_name = 'Иванов';
```

## 4. Удаление данных (`DELETE`)

Оператор `DELETE` удаляет записи из таблицы.

```sql
DELETE FROM users WHERE age < 18;
```

## 5. Создание таблицы (`CREATE TABLE`)

Создадим таблицу `users` с основными полями:

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    age INT
);
```

## 6. Удаление таблицы (`DROP TABLE`)

Если нужно удалить таблицу, используем:

```sql
DROP TABLE users;
```
