FastAPI — это высокопроизводительный фреймворк для создания API на Python. Он поддерживает асинхронность, автоматическое создание документации и интеграцию с различными инструментами. В этом разделе мы рассмотрим более продвинутые возможности FastAPI, включая мидлвары, CORS и другие настройки.

### 1. Мидлвары (Middleware)

Мидлвары в FastAPI позволяют обрабатывать запросы и ответы на более высоком уровне, до того как они попадут в обработчики маршрутов (или после их выполнения). Мидлварь может быть использована для логирования, авторизации, обработки ошибок и других целей.

#### Пример мидлвари для логирования:
```python
from fastapi import FastAPI
from starlette.middleware.base import BaseHTTPMiddleware
import logging

app = FastAPI()

# Настройка логирования
logging.basicConfig(level=logging.INFO)

class LogMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request, call_next):
        logging.info(f"Запрос: {request.method} {request.url}")
        response = await call_next(request)
        return response

# Добавляем мидлварь в приложение
app.add_middleware(LogMiddleware)

@app.get("/")
async def read_root():
    return {"message": "Hello, FastAPI!"}
````

В этом примере мидлварь будет записывать в лог информацию о каждом запросе.

### 2. CORS (Cross-Origin Resource Sharing)

CORS — это механизм, который позволяет браузерам делать запросы к ресурсам, находящимся на других доменах, помимо того, на котором размещен веб-сайт. В FastAPI для этого используется библиотека `CORSMiddleware`.

#### Пример настройки CORS:

```python
from fastapi import FastAPI
from starlette.middleware.cors import CORSMiddleware

app = FastAPI()

# Разрешаем CORS для всех доменов
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # Разрешаем все домены
    allow_credentials=True,
    allow_methods=["*"],  # Разрешаем все HTTP методы
    allow_headers=["*"],  # Разрешаем все заголовки
)

@app.get("/")
async def read_root():
    return {"message": "Hello, FastAPI with CORS!"}
```

В данном примере CORS настроен так, что разрешены запросы от любых источников, что полезно для разработки и работы с различными фронтенд-приложениями.

### 3. Параметры зависимости и инъекция зависимостей

FastAPI поддерживает зависимостями на уровне функций. Это позволяет внедрять общие компоненты, такие как базы данных, конфигурации, авторизацию и другие объекты, которые могут быть повторно использованы в разных маршрутах.

#### Пример зависимости для подключения к базе данных:

```python
from fastapi import FastAPI, Depends

app = FastAPI()

# Пример зависимости для работы с базой данных
def get_db():
    db = "Database connection"  # Подключение к базе данных
    try:
        yield db
    finally:
        db = None  # Закрытие соединения

@app.get("/items/")
async def get_items(db: str = Depends(get_db)):
    return {"message": f"Using {db} to fetch items"}
```

В этом примере зависимость `get_db` предоставляет подключение к базе данных для каждого запроса.

### 4. Фоновые задачи (Background Tasks)

FastAPI поддерживает асинхронные фоновые задачи, которые выполняются после отправки ответа клиенту. Это полезно для задач, которые не требуют немедленного ответа, таких как отправка электронной почты или обработка данных.

#### Пример фоновой задачи:

```python
from fastapi import FastAPI, BackgroundTasks
import time

app = FastAPI()

def write_log(message: str):
    time.sleep(5)  # Симуляция долгой работы
    with open("log.txt", mode="a") as log:
        log.write(message)

@app.get("/")
async def root(background_tasks: BackgroundTasks):
    background_tasks.add_task(write_log, "Запрос был обработан!")
    return {"message": "Запрос отправлен на обработку в фоновом режиме."}
```

В этом примере функция `write_log` выполняется в фоновом режиме, а клиент сразу получает ответ.

### 5. Работа с WebSocket

FastAPI поддерживает WebSocket, который позволяет устанавливать двустороннее соединение между клиентом и сервером. WebSocket может быть полезен для создания реального времени чат-приложений, уведомлений или других интерактивных приложений.

#### Пример использования WebSocket:

```python
from fastapi import FastAPI, WebSocket

app = FastAPI()

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    while True:
        data = await websocket.receive_text()
        await websocket.send_text(f"Сообщение получено: {data}")
```

В этом примере создается WebSocket-эндпоинт, который обрабатывает двустороннюю передачу данных.

### 6. Авторизация с OAuth2 и JWT

FastAPI интегрируется с OAuth2 и JWT (JSON Web Tokens) для обработки авторизации. С помощью зависимости `Depends` можно легко защитить маршруты.

#### Пример авторизации с JWT:

```python
from fastapi import FastAPI, Depends, HTTPException
from fastapi.security import OAuth2PasswordBearer
from jose import JWTError, jwt
from datetime import datetime, timedelta

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

# Секретный ключ для JWT
SECRET_KEY = "mysecretkey"
ALGORITHM = "HS256"

# Модели для пользователя
class User:
    def __init__(self, username: str):
        self.username = username

# Функция для верификации JWT
def verify_token(token: str):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        return User(payload["sub"])
    except JWTError:
        raise HTTPException(status_code=401, detail="Invalid token")

@app.get("/users/me")
async def read_users_me(current_user: User = Depends(verify_token)):
    return {"username": current_user.username}
```

Здесь реализована базовая авторизация с использованием JWT. Данный пример верифицирует токен, который передается в заголовке `Authorization`.

### Заключение

FastAPI предлагает мощные и гибкие инструменты для работы с API. Мидлвары позволяют обрабатывать запросы на глобальном уровне, CORS настраивает политику доступа к ресурсам из разных источников, а фоновые задачи дают возможность асинхронно обрабатывать длительные операции. Также фреймворк поддерживает авторизацию с использованием OAuth2 и JWT, а работа с WebSocket открывает возможности для создания приложений реального времени.