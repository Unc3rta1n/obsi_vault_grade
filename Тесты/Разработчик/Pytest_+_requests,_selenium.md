### 1. Pytest + Requests

**Pytest** — это популярный фреймворк для тестирования на Python, который предлагает удобный и мощный способ написания тестов, включая поддержку fixtures, параметризации и ассертирование.

**Requests** — это библиотека для работы с HTTP-запросами, которая часто используется в тестах для взаимодействия с веб-сервисами и API.

Пример теста с использованием `pytest` и `requests`:

```python
import pytest
import requests

def test_get_status_code():
    url = "https://jsonplaceholder.typicode.com/posts"
    response = requests.get(url)
    assert response.status_code == 200

def test_create_post():
    url = "https://jsonplaceholder.typicode.com/posts"
    data = {"title": "foo", "body": "bar", "userId": 1}
    response = requests.post(url, json=data)
    assert response.status_code == 201
    assert response.json()["title"] == "foo"
````

Здесь тестируются GET и POST запросы с использованием библиотеки `requests` и фреймворка `pytest`. Тесты проверяют, что ответы от сервера имеют ожидаемый статус-код и содержат необходимые данные.

### 2. Selenium

**Selenium** — это инструмент для автоматизации взаимодействия с веб-браузером. Он используется для функционального тестирования веб-приложений, а также для выполнения end-to-end тестов.

Пример использования Selenium для тестирования:

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys

def test_google_search():
    driver = webdriver.Chrome()  # Запуск браузера Chrome
    driver.get("https://www.google.com")

    search_box = driver.find_element(By.NAME, "q")
    search_box.send_keys("Selenium")
    search_box.send_keys(Keys.RETURN)

    assert "Selenium" in driver.title

    driver.quit()
```

В этом примере используется `Selenium` для автоматизации поисковой операции на Google. Мы открываем сайт, находим поле поиска, вводим запрос и проверяем, что в заголовке страницы присутствует слово "Selenium".

### Резюме

- **Pytest** — это удобный и мощный фреймворк для тестирования в Python, который позволяет эффективно писать тесты и проверять функциональность.
- **Requests** — используется для тестирования API и HTTP-запросов.
- **Selenium** — инструмент для автоматизации тестирования веб-приложений через взаимодействие с браузером.

Эти инструменты помогают создавать и автоматизировать тесты для различных типов приложений и проверок, что способствует улучшению качества программного обеспечения.