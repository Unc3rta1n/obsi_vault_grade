## Основы работы с FastAPI (расширенное)

FastAPI — это один из самых быстрых фреймворков для создания API на Python, и его основное преимущество — это скорость разработки, благодаря автоматической генерации документации и строгим типам данных. Он также поддерживает асинхронность, что позволяет создавать высокопроизводительные приложения. В этом разделе мы рассмотрим более интересные и полезные возможности FastAPI.

### 1. Обработка запросов с различными методами

FastAPI позволяет обрабатывать запросы различных типов: `GET`, `POST`, `PUT`, `DELETE`. Пример для обработки `PUT` и `DELETE` запросов:

#### PUT-запрос:
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: str = None
    price: float

@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):
    return {"item_id": item_id, "name": item.name, "price": item.price}
````

Этот маршрут позволяет обновлять данные о товаре. Отправив `PUT` запрос с телом JSON, можно обновить название или цену.

#### DELETE-запрос:

```python
@app.delete("/items/{item_id}")
async def delete_item(item_id: int):
    return {"message": f"Item {item_id} has been deleted."}
```

Удаление записи по ID. Запрос по адресу `DELETE /items/{item_id}` удалит товар с указанным ID.

### 2. Асинхронные запросы

FastAPI поддерживает асинхронные функции, которые позволяют обрабатывать запросы без блокировки сервера. Это особенно полезно для I/O операций, таких как взаимодействие с базой данных или внешними API.

Пример асинхронной функции с задержкой:

```python
import asyncio

@app.get("/long-process")
async def long_process():
    await asyncio.sleep(5)  # Симуляция долгой операции
    return {"message": "Process completed"}
```

В данном примере сервер не блокирует выполнение других запросов на время задержки, а просто выполняет задачу асинхронно.

### 3. Валидаторы данных с Pydantic

FastAPI тесно интегрирован с Pydantic для валидации данных, что позволяет вам создавать четко определенные структуры данных с проверками типов и значений.

#### Пример валидации с Pydantic:

```python
from pydantic import BaseModel, validator

class Item(BaseModel):
    name: str
    price: float
    description: str = None

    @validator('price')
    def check_price(cls, value):
        if value <= 0:
            raise ValueError('Price must be greater than zero')
        return value
```

В этом примере мы добавили валидатор для проверки цены товара, чтобы она была больше нуля. Если передано значение меньше или равно нулю, будет выброшено исключение.

### 4. Пример использования зависимости (Depends)

FastAPI поддерживает механизм зависимостей через `Depends`, который помогает разделить логику и повторно использовать код. Например, можно создать функцию для аутентификации, которую будет использовать несколько маршрутов.

#### Пример зависимости для аутентификации:

```python
from fastapi import Depends, HTTPException, status

def verify_token(token: str):
    if token != "secret-token":
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Invalid token")
    return token

@app.get("/secure-data/")
def get_secure_data(token: str = Depends(verify_token)):
    return {"message": "You have access to secure data!"}
```

В этом примере функция `verify_token` проверяет, валиден ли переданный токен, и если нет — выбрасывает ошибку. Маршрут `/secure-data/` использует эту зависимость, чтобы обеспечивать доступ только при наличии правильного токена.

### 5. Тестирование с FastAPI

FastAPI упрощает тестирование. Мы можем использовать `TestClient` для создания тестов. Например, с помощью pytest можно протестировать наше API.

#### Пример теста с использованием `TestClient`:

```python
from fastapi.testclient import TestClient

client = TestClient(app)

def test_read_root():
    response = client.get("/")
    assert response.status_code == 200
    assert response.json() == {"message": "Hello, FastAPI!"}

def test_create_item():
    response = client.post("/items/", json={"name": "Item 1", "price": 20.5})
    assert response.status_code == 200
    assert response.json() == {"item": "Item 1", "price": 20.5}
```

С помощью `TestClient` можно отправлять запросы к вашему приложению и проверять, что сервер правильно обрабатывает запросы.

### 6. Безопасность и авторизация

FastAPI предоставляет различные инструменты для работы с безопасностью, включая OAuth2, JWT и другие механизмы аутентификации.

#### Пример использования OAuth2 с паролем и токеном:

```python
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

def get_current_user(token: str = Depends(oauth2_scheme)):
    if token != "valid-token":
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Invalid token")
    return {"user": "admin"}

@app.get("/users/me")
def read_users_me(current_user: dict = Depends(get_current_user)):
    return current_user
```

Здесь используется схема OAuth2 для авторизации, и только пользователи с действительным токеном могут получить доступ к защищенным маршрутам.

### 7. Генерация документации

FastAPI автоматически генерирует документацию с помощью Swagger UI и ReDoc. Документация доступна по адресу:

- Swagger UI: `/docs`
- ReDoc: `/redoc`

Swagger позволяет вам тестировать эндпоинты непосредственно из документации, а ReDoc предоставляет красивую и подробную документацию API.

### Заключение

FastAPI — это мощный инструмент для создания высокопроизводительных API. Он позволяет легко работать с асинхронностью, валидацией данных, зависимостями и безопасностью. Интеграция с Pydantic и поддержка автоматической документации делают его идеальным выбором для разработки современных веб-приложений. FastAPI также упрощает тестирование и обеспечивает удобную работу с OAuth2 и другими механизмами безопасности.