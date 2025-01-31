SQLAlchemy — это популярная ORM (Object-Relational Mapping) библиотека для Python, которая предоставляет инструменты для работы с реляционными базами данных, абстрагируя SQL-запросы в объекты Python. Это позволяет работать с базой данных, используя объекты и методы, а не писать SQL-запросы вручную.

SQLAlchemy состоит из двух основных частей:
1. **Core** — низкоуровневый слой для работы с базой данных, включающий создание таблиц и выполнение SQL-запросов.
2. **ORM** — высокоуровневый слой, предоставляющий работу с базой данных через объекты Python, что упрощает процесс работы с данными.

### 1. Установка SQLAlchemy

Для начала работы с SQLAlchemy необходимо установить библиотеку:

```bash
pip install sqlalchemy
````

### 2. Создание базовой модели

В SQLAlchemy модели описываются как классы, наследующие от `Base`. Каждый класс представляет таблицу в базе данных.

```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

Base = declarative_base()

class User(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True)
    name = Column(String)
    age = Column(Integer)

# Создание базы данных (или подключение к существующей)
engine = create_engine('sqlite:///example.db')

# Создание таблиц
Base.metadata.create_all(engine)
```

### 3. Работа с сессиями

Для взаимодействия с базой данных используется объект **Session**, который управляет транзакциями и выполняет запросы.

```python
Session = sessionmaker(bind=engine)
session = Session()

# Добавление нового пользователя
new_user = User(name="John Doe", age=30)
session.add(new_user)
session.commit()

# Получение всех пользователей
users = session.query(User).all()
print(users)
```

### 4. Запросы с фильтрацией и сортировкой

SQLAlchemy позволяет фильтровать данные и выполнять сортировку с использованием методов на объекте запроса.

```python
# Получить пользователя по имени
user = session.query(User).filter(User.name == "John Doe").first()
print(user)

# Получить пользователей старше 25 лет, отсортированных по возрасту
users = session.query(User).filter(User.age > 25).order_by(User.age).all()
print(users)
```

### 5. Обновление и удаление данных

SQLAlchemy также предоставляет способы для обновления и удаления данных в базе.

```python
# Обновление данных
user = session.query(User).filter(User.name == "John Doe").first()
user.age = 31
session.commit()

# Удаление данных
session.query(User).filter(User.name == "John Doe").delete()
session.commit()
```

### 6. Работа с отношениями (Foreign Keys)

SQLAlchemy поддерживает создание отношений между таблицами через внешние ключи (Foreign Key).

```python
from sqlalchemy import ForeignKey
from sqlalchemy.orm import relationship

class Address(Base):
    __tablename__ = 'addresses'

    id = Column(Integer, primary_key=True)
    email_address = Column(String, nullable=False)
    user_id = Column(Integer, ForeignKey('users.id'))

    user = relationship("User", back_populates="addresses")

User.addresses = relationship("Address", order_by=Address.id, back_populates="user")
```

### 7. Обработка ошибок

При работе с SQLAlchemy важно учитывать возможные ошибки, такие как нарушение уникальности, ошибки подключения и т.д.

```python
from sqlalchemy.exc import IntegrityError

try:
    new_user = User(name="Jane Doe", age=25)
    session.add(new_user)
    session.commit()
except IntegrityError:
    session.rollback()
    print("Ошибка при добавлении данных")
```

### Заключение

SQLAlchemy является мощным инструментом для работы с реляционными базами данных в Python. Основы включают создание моделей, работу с сессиями, выполнение базовых SQL-запросов и использование отношений между таблицами. С этим знанием можно легко начать использовать SQLAlchemy для создания полноценных приложений с базами данных.