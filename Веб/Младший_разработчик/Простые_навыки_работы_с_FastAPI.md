
FastAPI — это фреймворк для создания API на Python, который отличается высокой производительностью и удобством в использовании. Он поддерживает аннотации типов и автоматическую документацию. Рассмотрим базовые навыки для начала работы с FastAPI.

### 1. Установка и создание базового приложения

Для начала нужно установить FastAPI и Uvicorn:

```bash
pip install fastapi uvicorn
````

Создадим файл `main.py` с базовым приложением:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello, FastAPI!"}
```

Запускаем сервер с помощью Uvicorn:

```bash
uvicorn main:app --reload
```

Теперь ваше приложение доступно по адресу `http://127.0.0.1:8000`.

### 2. Обработка параметров URL

FastAPI позволяет удобно работать с параметрами URL. Пример с параметром в URL:

```python
@app.get("/greet/{name}")
def greet_user(name: str):
    return {"message": f"Hello, {name}!"}
```

Запрос по адресу `http://127.0.0.1:8000/greet/John` вернет: `{"message": "Hello, John!"}`

### 3. Обработка параметров запроса

Вы также можете передавать параметры через строку запроса. Например:

```python
@app.get("/items/")
def read_item(q: str = None, skip: int = 0, limit: int = 10):
    return {"q": q, "skip": skip, "limit": limit}
```

Запрос `http://127.0.0.1:8000/items/?q=test&skip=5&limit=10` вернет:

```json
{
  "q": "test",
  "skip": 5,
  "limit": 10
}
```

### 4. Работа с POST запросами

FastAPI также позволяет работать с POST-запросами. Для этого можно использовать модели Pydantic.

Пример создания модели:

```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: str = None
    price: float
    tax: float = None
```

Обработчик для POST-запроса:

```python
@app.post("/items/")
def create_item(item: Item):
    return {"item": item.name, "price": item.price}
```

Запрос с телом JSON:

```json
{
  "name": "Item 1",
  "description": "A sample item",
  "price": 25.5
}
```

### 5. Автоматическая документация

FastAPI генерирует документацию для вашего API автоматически. Вы можете использовать Swagger UI для взаимодействия с API через браузер. Документация доступна по адресу:

```
http://127.0.0.1:8000/docs
```

Swagger UI позволяет вам тестировать все доступные эндпоинты API напрямую из браузера.

### Заключение

FastAPI позволяет быстро и удобно создавать API с минимальными усилиями. С помощью аннотаций типов и Pydantic моделей можно обрабатывать запросы, автоматически генерировать документацию и легко тестировать API. Это базовые навыки, которые помогут вам начать работать с FastAPI.