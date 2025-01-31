Продвинутые SQL-запросы позволяют эффективно работать с большими объемами данных, использовать сложные алгоритмы и оптимизировать производительность. Эти запросы включают использование оконных функций, сложных агрегатных операций, подзапросов, объединений и других методов для решения более сложных задач.

## 1. Оконные функции

Оконные функции выполняются над набором строк, определённым в окне, без агрегации этих строк. Они позволяют делать расчеты по каждой строке с учётом других строк в наборе.

### Пример использования оконной функции:
```sql
SELECT customer_id, order_date, 
       ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC) AS row_num
FROM orders;
```

В этом примере для каждого `customer_id` будет вычислен порядковый номер заказа (`ROW_NUMBER()`), начиная с самого последнего (сортировка по дате).

**Основные оконные функции**:
- `ROW_NUMBER()` — присваивает уникальный номер строкам в пределах окна.
- `RANK()` — присваивает ранг строкам с одинаковыми значениями.
- `DENSE_RANK()` — присваивает ранг, без пропусков, даже если есть одинаковые значения.
- `NTILE(n)` — делит набор строк на `n` частей и возвращает номер группы для каждой строки.
- `SUM()`, `AVG()`, `COUNT()` — агрегационные функции, которые выполняются по каждой строке в окне.

### Пример использования `SUM()` с оконной функцией:
```sql
SELECT customer_id, order_date, 
       SUM(order_amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS cumulative_sum
FROM orders;
```

В данном случае для каждого заказа будет вычислена накопительная сумма (`cumulative_sum`) для каждого клиента.

## 2. Подзапросы

Подзапросы могут быть использованы для более сложных выборок данных и предоставляют гибкость при выполнении запросов.

### Пример подзапроса в `WHERE`:
```sql
SELECT name 
FROM customers
WHERE id = (SELECT customer_id FROM orders WHERE order_date = '2024-01-01');
```

В этом запросе подзапрос выбирает `customer_id` из таблицы `orders`, и основной запрос использует это значение для выбора соответствующего имени из таблицы `customers`.

### Пример подзапроса в `FROM`:
```sql
SELECT AVG(order_amount) 
FROM (SELECT order_amount FROM orders WHERE customer_id = 123) AS customer_orders;
```

Здесь подзапрос используется для выборки всех заказов для определённого клиента, а затем вычисляется их среднее значение.

### Пример подзапроса с `IN`:
```sql
SELECT name 
FROM customers 
WHERE id IN (SELECT customer_id FROM orders WHERE order_date > '2024-01-01');
```

Подзапрос возвращает список клиентов, сделавших заказы после определённой даты.

## 3. Сложные соединения (`JOIN`)

Соединения позволяют объединить несколько таблиц в одном запросе. В SQL существуют разные виды соединений, включая внутренние (`INNER JOIN`), внешние (`LEFT JOIN`, `RIGHT JOIN`), а также полные соединения (`FULL OUTER JOIN`).

### Пример использования `INNER JOIN`:
```sql
SELECT customers.name, orders.order_date
FROM customers
INNER JOIN orders ON customers.id = orders.customer_id
WHERE orders.order_date > '2024-01-01';
```

Это соединение позволяет получить имена клиентов и даты заказов, сделанных после определённой даты.

### Пример использования `LEFT JOIN`:
```sql
SELECT customers.name, orders.order_date
FROM customers
LEFT JOIN orders ON customers.id = orders.customer_id;
```

В данном случае все клиенты будут включены в результат, даже если у них нет заказов. Для клиентов без заказов значение в столбце `order_date` будет `NULL`.

### Пример использования `FULL OUTER JOIN`:
```sql
SELECT customers.name, orders.order_date
FROM customers
FULL OUTER JOIN orders ON customers.id = orders.customer_id;
```

Этот запрос вернёт все строки из обеих таблиц, даже если не существует совпадений между таблицами.

## 4. Использование `WITH` (Common Table Expressions, CTE)

CTE позволяют создавать временные результаты, которые могут быть использованы в запросах, улучшая читаемость и структуру запросов.

### Пример использования CTE:
```sql
WITH recent_orders AS (
    SELECT customer_id, order_date, order_amount
    FROM orders
    WHERE order_date > '2024-01-01'
)
SELECT customers.name, SUM(recent_orders.order_amount) AS total_spent
FROM customers
JOIN recent_orders ON customers.id = recent_orders.customer_id
GROUP BY customers.name;
```

Здесь создаётся CTE `recent_orders`, который выбирает заказы после определённой даты. Затем основной запрос использует этот CTE для вычисления общей суммы покупок для каждого клиента.

## 5. Множественные агрегатные функции

Агрегатные функции позволяют выполнять вычисления на наборе строк. Например, можно одновременно подсчитать количество записей, найти сумму и среднее значение в одном запросе.

### Пример:
```sql
SELECT customer_id, COUNT(*) AS total_orders, SUM(order_amount) AS total_spent, AVG(order_amount) AS avg_order_value
FROM orders
GROUP BY customer_id;
```

Этот запрос возвращает количество заказов, общую сумму покупок и среднее значение заказа для каждого клиента.

## 6. Использование `CASE` для условной логики

`CASE` позволяет выполнять условные вычисления внутри запроса, что может быть полезно для категоризации данных или выполнения вычислений с условием.

### Пример использования `CASE`:
```sql
SELECT customer_id, order_amount,
       CASE 
           WHEN order_amount > 100 THEN 'High'
           WHEN order_amount BETWEEN 50 AND 100 THEN 'Medium'
           ELSE 'Low'
       END AS order_size
FROM orders;
```

Этот запрос классифицирует заказы в зависимости от суммы заказа: "High", "Medium" или "Low".

## 7. Использование `UNION` и `INTERSECT`

Операторы `UNION` и `INTERSECT` позволяют объединять результаты нескольких запросов.

- **`UNION`** объединяет результаты двух запросов, устраняя дубликаты.
- **`INTERSECT`** возвращает только те строки, которые присутствуют в обоих запросах.

### Пример использования `UNION`:
```sql
SELECT name FROM customers WHERE customer_id < 100
UNION
SELECT name FROM customers WHERE customer_id > 200;
```

### Пример использования `INTERSECT`:
```sql
SELECT customer_id FROM orders WHERE order_date > '2024-01-01'
INTERSECT
SELECT customer_id FROM orders WHERE order_amount > 500;
```

## 8. Использование `DISTINCT`

Оператор `DISTINCT` используется для исключения дублирующихся строк из результата запроса.

### Пример:
```sql
SELECT DISTINCT customer_id FROM orders WHERE order_date > '2024-01-01';
```

Этот запрос вернёт уникальные `customer_id` для клиентов, сделавших заказы после 1 января 2024 года.

## Заключение

Продвинутые SQL-запросы позволяют решать более сложные задачи, такие как работа с окнами, агрегатными функциями, подзапросами и объединениями. Они дают возможность гибко работать с большими объемами данных, выполнять сложные вычисления и агрегации, а также оптимизировать производительность запросов. Понимание этих инструментов позволяет значительно повысить эффективность работы с реляционными базами данных.
