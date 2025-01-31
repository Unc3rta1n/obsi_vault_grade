SQLAlchemy предоставляет не только основные средства работы с базами данных, но и мощные инструменты для выполнения сложных запросов, создания алиасов, работы с подзапросами, агрегациями и многими другими возможностями. В этой части мы рассмотрим продвинутые аспекты работы с SQLAlchemy, которые помогут вам эффективно взаимодействовать с базой данных на более высоком уровне.

### 1. Использование алиасов в запросах

Алиасы полезны для работы с таблицами, когда необходимо сделать ссылки на одну и ту же таблицу несколько раз в одном запросе, например, при выполнении самообслуживания (self-join).

#### Пример с использованием алиаса:

```python
from sqlalchemy.orm import aliased
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine, select
from models import Parent, Child  # Предположим, что модели Parent и Child определены

# Создание сессии
engine = create_engine('sqlite:///example.db')
Session = sessionmaker(bind=engine)

# Создание алиаса для модели Child
child_alias = aliased(Child)

# Запрос, использующий алиас
with Session() as session:
    results = session.query(Parent, child_alias).join(child_alias, Parent.id == child_alias.parent_id).all()
    for parent, child in results:
        print(f"Parent: {parent.name}, Child: {child.name}")
````

В этом примере создается алиас для модели `Child`, чтобы можно было использовать её несколько раз в одном запросе.

---

### 2. Подзапросы и использование `subquery()`

Подзапросы позволяют интегрировать более сложную логику в ваш запрос, когда требуется выполнить вложенные запросы или фильтры.

#### Пример подзапроса:

```python
from sqlalchemy.orm import aliased
from sqlalchemy import select

# Предположим, что в модели MyModel есть поле 'id' и 'price'
subquery = select([MyModel.id, MyModel.price]).where(MyModel.price > 1000).subquery()

# Использование подзапроса в основном запросе
with Session() as session:
    results = session.query(MyModel).join(subquery, MyModel.id == subquery.c.id).all()
    for result in results:
        print(f"ID: {result.id}, Price: {result.price}")
```

Подзапросы могут быть полезны для более сложных фильтраций или агрегаций, когда необходимо сначала выполнить запрос, а затем использовать результаты внутри другого запроса.

---

### 3. Группировка и агрегация данных

SQLAlchemy позволяет удобно использовать агрегации (например, `COUNT`, `SUM`, `AVG`) в запросах, что полезно для анализа данных.

#### Пример группировки и агрегации:

```python
from sqlalchemy import func

# Запрос с группировкой и подсчетом
with Session() as session:
    results = session.query(MyModel.category, func.count(MyModel.id)).group_by(MyModel.category).all()
    for category, count in results:
        print(f"Category: {category}, Count: {count}")
```

Здесь выполняется группировка по полю `category` и подсчет числа записей в каждой категории.

---

### 4. Использование оконных функций (Window Functions)

Оконные функции позволяют вычислять агрегированные значения на основе группировки, но в отличие от обычных агрегатов, они не сводят строки в одну. Это полезно, например, для вычисления рангов или скользящих средних.

#### Пример использования оконных функций:

```python
from sqlalchemy import func, over

# Запрос с оконной функцией
with Session() as session:
    results = session.query(
        MyModel.id,
        MyModel.price,
        func.rank().over(order_by=MyModel.price.desc()).label("rank")
    ).all()

    for result in results:
        print(f"ID: {result.id}, Price: {result.price}, Rank: {result.rank}")
```

Здесь для каждого элемента вычисляется его ранг на основе значения поля `price`.

---

### 5. Использование `exists()` для проверки существования данных

Функция `exists()` полезна для выполнения проверки существования данных в базе без извлечения самих данных. Это может быть полезно, например, для фильтрации или условных проверок.

#### Пример использования `exists()`:

```python
from sqlalchemy import exists

# Запрос с использованием exists()
with Session() as session:
    subquery = exists().where(MyModel.id == 1)
    results = session.query(MyModel).filter(subquery).all()

    for result in results:
        print(f"ID: {result.id}, Name: {result.name}")
```

Здесь выполняется проверка, существует ли объект с id = 1, прежде чем извлекать данные.

---

### 6. Сложные объединения и `join()`

SQLAlchemy поддерживает несколько типов объединений (JOIN), что позволяет выполнять более сложные запросы с объединением таблиц.

#### Пример сложного соединения:

```python
from sqlalchemy.orm import joinedload

# Запрос с использованием join для нескольких таблиц
with Session() as session:
    results = session.query(Parent).join(Child).filter(Child.age > 10).all()

    for parent in results:
        print(f"Parent: {parent.name}, Children count: {len(parent.children)}")
```

Здесь используется фильтрация детей, возраст которых больше 10, при этом извлекаются все родители, у которых есть такие дети.

---

### 7. Сортировка и лимитирование данных

SQLAlchemy позволяет легко ограничивать количество результатов или сортировать их.

#### Пример сортировки и лимитирования:

```python
# Запрос с сортировкой и лимитированием
with Session() as session:
    results = session.query(MyModel).order_by(MyModel.price.desc()).limit(10).all()
    for result in results:
        print(f"ID: {result.id}, Price: {result.price}")
```

Здесь сортировка выполняется по убыванию цены, и ограничивается вывод десятью записями.

---

### 8. Использование `selectinload` для оптимизации запросов

Если нужно выполнить подгрузку связанных объектов, но избежать N+1 проблемы, можно использовать `selectinload`. Это позволяет эффективно загружать связанные данные.

#### Пример с `selectinload`:

```python
from sqlalchemy.orm import selectinload

# Запрос с подгрузкой связанных объектов
with Session() as session:
    results = session.query(Parent).options(selectinload(Parent.children)).all()
    for parent in results:
        print(f"Parent: {parent.name}, Children count: {len(parent.children)}")
```

Здесь `selectinload` подгружает всех детей для каждого родителя за один запрос.

---

### Заключение

Работа с SQLAlchemy предоставляет богатый набор возможностей для выполнения сложных и эффективных запросов. Использование алиасов, подзапросов, оконных функций и оптимизация запросов с помощью `selectinload` помогает эффективно работать с большими объемами данных и создавать сложные аналитические приложения. Эти продвинутые техники помогут вам более гибко и мощно работать с реляционными базами данных.