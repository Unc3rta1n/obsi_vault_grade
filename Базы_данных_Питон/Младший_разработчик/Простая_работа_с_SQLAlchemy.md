
### 1. Базовое использование SQLAlchemy

#### Определение модели

В SQLAlchemy таблицы создаются как классы, наследующие от `Base`.

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

# Создание базы данных
engine = create_engine('sqlite:///example.db')

# Создание таблиц в базе данных
Base.metadata.create_all(engine)
```

#### Создание сессии

Для выполнения операций с базой данных используется сессия.

```python
Session = sessionmaker(bind=engine)
session = Session()

# Добавление нового пользователя
new_user = User(name="John Doe", age=30)
session.add(new_user)
session.commit()

# Получение пользователей
users = session.query(User).all()
print(users)
```

### 2. Асинхронная работа с SQLAlchemy

Асинхронная работа с SQLAlchemy позволяет улучшить производительность, особенно при работе с большим количеством одновременных соединений и операций.

#### Определение асинхронной модели

Для асинхронных операций SQLAlchemy имеет поддержку через `AsyncSession` и асинхронный движок:

```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker

Base = declarative_base()

class User(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True)
    name = Column(String)
    age = Column(Integer)

# Создание асинхронного двигателя
engine = create_async_engine('postgresql+asyncpg://user:password@localhost/dbname', echo=True)

# Создание асинхронной сессии
AsyncSessionLocal = sessionmaker(
    bind=engine,
    class_=AsyncSession,
    expire_on_commit=False,
)

# Асинхронная сессия
async def async_add_user(name: str, age: int):
    async with AsyncSessionLocal() as session:
        async with session.begin():
            new_user = User(name=name, age=age)
            session.add(new_user)
            await session.commit()

# Вызов асинхронной функции
import asyncio
asyncio.run(async_add_user('Jane Doe', 25))
```

### 3. Асинхронный запрос данных

Асинхронные запросы с использованием `AsyncSession` и `await` позволяют работать с базой данных без блокировки потока:

```python
async def async_get_users():
    async with AsyncSessionLocal() as session:
        result = await session.execute("SELECT * FROM users")
        users = result.fetchall()
        print(users)

# Вызов асинхронной функции
asyncio.run(async_get_users())
```

### 4. Асинхронные транзакции

При выполнении асинхронных операций важно использовать транзакции, чтобы гарантировать целостность данных.

```python
async def async_update_user(user_id: int, new_age: int):
    async with AsyncSessionLocal() as session:
        async with session.begin():
            user = await session.get(User, user_id)
            user.age = new_age
            await session.commit()
```

### Заключение

SQLAlchemy предоставляет мощный и гибкий инструмент для работы с базами данных, позволяя эффективно использовать как синхронный, так и асинхронный подход в работе с реляционными данными. Асинхронная работа с SQLAlchemy позволяет значительно улучшить производительность приложения при работе с большим количеством запросов и транзакций.

Для перехода к асинхронной работе требуется использовать `AsyncSession`, асинхронный движок `create_async_engine` и другие компоненты, которые помогут вам эффективно работать с базами данных в асинхронных приложениях.