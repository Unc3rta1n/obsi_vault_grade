
FastAPI — это современный, быстрый (high-performance) веб-фреймворк для создания API с использованием Python 3.6+ и стандартов типа аннотаций. Он разработан с акцентом на производительность и автоматическое создание документации.

### 1. Установка FastAPI и Uvicorn

Для начала работы с FastAPI необходимо установить сам фреймворк и ASGI сервер (например, Uvicorn):

```bash
pip install fastapi
pip install uvicorn
````

### 2. Простейший пример с FastAPI

Создадим минимальный сервер с одним эндпоинтом для получения приветственного сообщения.

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello, FastAPI!"}
```

Чтобы запустить сервер, используем Uvicorn:

```bash
uvicorn main:app --reload
```

Здесь:

- `main` — это имя Python файла.
- `app` — объект приложения FastAPI.
- `--reload` — флаг для автоматической перезагрузки при изменении кода.

### 3. Параметры запросов

FastAPI позволяет легко обрабатывать параметры запросов, такие как GET-параметры, JSON и формы.

#### Пример с параметрами URL

```python
@app.get("/greet/{name}")
def greet_user(name: str):
    return {"message": f"Hello, {name}!"}
```

#### Пример с параметрами запроса

```python
@app.get("/items/")
def read_item(q: str = None, skip: int = 0, limit: int = 10):
    return {"q": q, "skip": skip, "limit": limit}
```

### 4. Параметры в теле запроса (POST)

FastAPI поддерживает аннотации типов для работы с телом запроса. Для этого используем Pydantic модели.

#### Пример с POST-запросом и моделью Pydantic:

```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: str = None
    price: float
    tax: float = None

@app.post("/items/")
def create_item(item: Item):
    return {"item": item.name, "price": item.price}
```

### 5. Автоматическая документация

FastAPI автоматически генерирует документацию для всех эндпоинтов API. Используются два стандарта:

- **Swagger UI** — доступна по адресу `/docs`.
- **ReDoc** — доступна по адресу `/redoc`.

Документация генерируется на основе аннотаций типов и Pydantic моделей, что делает взаимодействие с API удобным и понятным.

### 6. Обработка ошибок

FastAPI позволяет обрабатывать ошибки с помощью исключений и предоставляет стандартные коды ошибок HTTP.

Пример обработки ошибки:

```python
from fastapi import HTTPException

@app.get("/items/{item_id}")
def read_item(item_id: int):
    if item_id not in items_db:
        raise HTTPException(status_code=404, detail="Item not found")
    return items_db[item_id]
```

### 7. Зависимости

FastAPI поддерживает систему зависимостей для внедрения сервисов и вспомогательных функций в эндпоинты.

Пример с зависимостью:

```python
from fastapi import Depends

def get_db():
    db = "database_connection"
    return db

@app.get("/items/")
def read_items(db: str = Depends(get_db)):
    return {"db_connection": db}
```

### 8. Работа с запросами и ответами

FastAPI позволяет удобно работать с запросами и ответами. Можно использовать как стандартные, так и кастомизированные модели для запросов и ответов.

Пример с кастомизированным ответом:

```python
from fastapi.responses import JSONResponse

@app.get("/custom-response/")
def custom_response():
    return JSONResponse(content={"message": "This is a custom response!"}, status_code=200)
```

### Заключение

FastAPI — это мощный инструмент для быстрого создания API с высокими показателями производительности. Он предоставляет отличные возможности для работы с типами, встроенную валидацию, автоматическую документацию и многое другое, что упрощает разработку и тестирование API.