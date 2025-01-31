## 1. Выборка данных с фильтрацией и сортировкой

### Фильтрация данных с несколькими условиями:
```sql
SELECT * FROM users WHERE age >= 18 AND last_name = 'Иванов';
```

### Использование `OR` для альтернативных условий:
```sql
SELECT * FROM users WHERE age < 18 OR last_name = 'Петров';
```

### Сортировка по нескольким столбцам:
```sql
SELECT * FROM users ORDER BY age DESC, last_name ASC;
```

## 2. Ограничение количества строк с `LIMIT`

### Получить только первые 5 строк:
```sql
SELECT * FROM users LIMIT 5;
```

### Пропустить первые 10 строк и получить следующие 5:
```sql
SELECT * FROM users LIMIT 5 OFFSET 10;
```

## 3. Агрегация данных

### Подсчет количества строк:
```sql
SELECT COUNT(*) FROM users WHERE age >= 18;
```

### Получение среднего возраста:
```sql
SELECT AVG(age) FROM users;
```

### Получение минимального и максимального значения:
```sql
SELECT MIN(age), MAX(age) FROM users;
```

### Группировка данных с использованием `GROUP BY`:
```sql
SELECT last_name, COUNT(*) FROM users GROUP BY last_name;
```

## 4. Пример с операциями на строках

### Конкатенация строк:
```sql
SELECT first_name || ' ' || last_name AS full_name FROM users;
```

### Преобразование в верхний регистр:
```sql
SELECT UPPER(first_name) FROM users;
```

### Извлечение подстроки:
```sql
SELECT SUBSTRING(first_name FROM 1 FOR 3) FROM users;
```

## 5. Объединение данных с `JOIN`

### Простое объединение двух таблиц:
```sql
SELECT users.first_name, orders.order_date
FROM users
JOIN orders ON users.id = orders.user_id;
```

### Левое объединение (LEFT JOIN):
```sql
SELECT users.first_name, orders.order_date
FROM users
LEFT JOIN orders ON users.id = orders.user_id;
```

## 6. Использование подзапросов

### Вложенный запрос в `WHERE`:
```sql
SELECT * FROM users WHERE age > (SELECT AVG(age) FROM users);
```

### Вложенный запрос в `FROM`:
```sql
SELECT avg_age
FROM (SELECT AVG(age) AS avg_age FROM users) AS subquery;
```

## Заключение

Простые SQL-запросы включают в себя не только базовые операции, такие как выборка, вставка, обновление и удаление данных, но и более сложные техники работы с данными: агрегация, сортировка, объединение таблиц и использование подзапросов. Эти возможности делают SQL мощным инструментом для работы с реляционными базами данных.
