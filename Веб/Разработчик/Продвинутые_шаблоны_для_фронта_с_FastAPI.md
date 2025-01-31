FastAPI поддерживает интеграцию с Jinja2, что позволяет создавать динамичные шаблоны на стороне сервера. Этот функционал полезен для реализации серверного рендеринга или для обработки данных, которые потом отправляются на клиент. В этой части рассмотрим более сложные возможности использования шаблонов, включая работу с фильтрами, шаблонными наследованиями и асинхронными рендерами.

### 1. Шаблонное наследование и включение

Одной из ключевых особенностей Jinja2 является возможность использования шаблонного наследования, что позволяет строить сложные страницы, разделяя их на общие компоненты (например, хедер, футер, боковые панели). Это улучшает поддерживаемость кода и упрощает работу с большими проектами.

#### Пример: Шаблон с наследованием

Создадим базовый шаблон, который будет включать хедер и футер на каждой странице.

##### `base.html`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}My FastAPI Application{% endblock %}</title>
</head>
<body>
    <header>
        <h1>My FastAPI Application</h1>
        <nav>
            <a href="/">Home</a> |
            <a href="/about">About</a>
        </nav>
    </header>
    <div>
        {% block content %}
        <!-- Content will go here -->
        {% endblock %}
    </div>
    <footer>
        <p>&copy; 2025 My FastAPI Application</p>
    </footer>
</body>
</html>
````

##### `home.html` (страница наследует базовый шаблон):

```html
{% extends "base.html" %}

{% block title %}Home - My FastAPI Application{% endblock %}

{% block content %}
    <h2>Welcome to the Home Page!</h2>
    <p>This is an example of a dynamic template with FastAPI.</p>
{% endblock %}
```

В этом примере `home.html` наследует `base.html`, переопределяя блоки `title` и `content`. Когда эта страница будет рендериться, FastAPI автоматически встраивает данные из `home.html` в соответствующие блоки шаблона `base.html`.

### 2. Передача сложных данных в шаблоны

FastAPI и Jinja2 позволяют передавать сложные структуры данных, такие как списки, словари и объекты, в шаблоны. Это даёт гибкость для работы с динамическим контентом.

#### Пример: Список объектов в шаблоне

Предположим, у нас есть список товаров, который нужно отобразить на странице:

```python
from fastapi import FastAPI
from fastapi.templating import Jinja2Templates
from starlette.requests import Request

app = FastAPI()
templates = Jinja2Templates(directory="templates")

# Пример данных
items = [
    {"name": "Item 1", "price": 25.5},
    {"name": "Item 2", "price": 15.0},
    {"name": "Item 3", "price": 30.99}
]

@app.get("/items/")
async def get_items(request: Request):
    return templates.TemplateResponse("items.html", {"request": request, "items": items})
```

##### `items.html`:

```html
{% extends "base.html" %}

{% block content %}
    <h2>Items List</h2>
    <ul>
        {% for item in items %}
            <li>{{ item.name }} - ${{ item.price }}</li>
        {% endfor %}
    </ul>
{% endblock %}
```

В этом примере мы передаем список объектов `items` в шаблон и отображаем их в виде списка на странице.

### 3. Фильтры Jinja2

Фильтры в Jinja2 позволяют обрабатывать данные перед их выводом в шаблон. Например, можно форматировать числа, даты или строки.

#### Пример использования фильтра:

```python
from fastapi import FastAPI
from fastapi.templating import Jinja2Templates
from starlette.requests import Request
from datetime import datetime

app = FastAPI()
templates = Jinja2Templates(directory="templates")

@app.get("/date/")
async def get_date(request: Request):
    return templates.TemplateResponse("date.html", {"request": request, "current_date": datetime.now()})
```

##### `date.html`:

```html
{% extends "base.html" %}

{% block content %}
    <h2>Current Date and Time</h2>
    <p>The current date and time is: {{ current_date | date("YYYY-MM-DD HH:mm:ss") }}</p>
{% endblock %}
```

В этом примере мы используем фильтр `date`, чтобы отформатировать текущую дату и время в удобочитаемый формат.

### 4. Асинхронный рендеринг шаблонов

В FastAPI можно использовать асинхронные шаблоны, что особенно полезно для рендеринга страниц с тяжелыми вычислениями или данными, загружаемыми из базы данных.

Чтобы рендерить шаблоны асинхронно, можно использовать `async def` и передавать данные в шаблон асинхронно.

```python
from fastapi import FastAPI
from fastapi.templating import Jinja2Templates
from starlette.requests import Request

app = FastAPI()
templates = Jinja2Templates(directory="templates")

@app.get("/async-page/")
async def async_page(request: Request):
    # Имитация асинхронной операции (например, запрос к базе данных)
    data = await fetch_data()
    return templates.TemplateResponse("async_page.html", {"request": request, "data": data})

async def fetch_data():
    await asyncio.sleep(2)  # Имитация задержки
    return {"message": "Data fetched asynchronously!"}
```

#### Пример `async_page.html`:

```html
{% extends "base.html" %}

{% block content %}
    <h2>{{ data.message }}</h2>
    <p>This page was rendered asynchronously!</p>
{% endblock %}
```

Этот пример показывает, как можно асинхронно загружать данные и рендерить их в шаблоне.

### 5. Защищенные страницы с авторизацией

FastAPI позволяет легко интегрировать систему аутентификации и авторизации с использованием шаблонов. Можно создавать защищенные страницы, доступные только после авторизации, и динамически отображать контент на основе прав пользователя.

#### Пример страницы с авторизацией:

```python
from fastapi import FastAPI, Depends, HTTPException
from fastapi.security import OAuth2PasswordBearer
from fastapi.templating import Jinja2Templates
from starlette.requests import Request

app = FastAPI()
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")
templates = Jinja2Templates(directory="templates")

def fake_decode_token(token: str):
    if token != "valid-token":
        raise HTTPException(status_code=401, detail="Invalid token")
    return {"username": "testuser"}

@app.get("/secure-page/")
async def secure_page(request: Request, token: str = Depends(oauth2_scheme)):
    user = fake_decode_token(token)
    return templates.TemplateResponse("secure_page.html", {"request": request, "user": user})
```

#### Пример `secure_page.html`:

```html
{% extends "base.html" %}

{% block content %}
    <h2>Welcome, {{ user.username }}!</h2>
    <p>This is a secure page.</p>
{% endblock %}
```

### Заключение

Использование продвинутых шаблонов с FastAPI и Jinja2 позволяет создавать динамичные веб-страницы с богатым контентом. Возможности шаблонного наследования, фильтры, асинхронные рендеры и поддержка безопасности позволяют разрабатывать гибкие и масштабируемые решения для вашего приложения.