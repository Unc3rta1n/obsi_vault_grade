### 1. Дополнительные возможности `unittest`

#### a) **Сопрограммы и мокирование**

`unittest` предоставляет полезный модуль `unittest.mock`, который позволяет создавать фальшивые объекты и замещать ими реальные объекты для тестирования. Это особенно полезно для изоляции тестов и замены сложных внешних зависимостей.

Пример использования мокирования:

```python
import unittest
from unittest.mock import MagicMock

class MyTest(unittest.TestCase):
    def test_mock(self):
        mock = MagicMock(return_value=42)
        self.assertEqual(mock(), 42)
````

#### b) **Тестирование с использованием `setUp` и `tearDown`**

Методы `setUp` и `tearDown` позволяют задать начальные условия для тестов и очистить ресурсы после их выполнения. Это полезно для подготовки тестовой среды.

Пример:

```python
import unittest

class MyTest(unittest.TestCase):
    def setUp(self):
        self.data = [1, 2, 3]

    def tearDown(self):
        self.data.clear()

    def test_sum(self):
        self.assertEqual(sum(self.data), 6)
```

#### c) **Асинхронные тесты**

Для тестирования асинхронного кода можно использовать `unittest.IsolatedAsyncioTestCase`, который предоставляет возможность тестировать асинхронные функции с помощью `async def`.

Пример:

```python
import unittest
import asyncio

class MyAsyncTest(unittest.IsolatedAsyncioTestCase):
    async def test_async_function(self):
        result = await asyncio.sleep(1, result=10)
        self.assertEqual(result, 10)
```

---

### 2. Дополнительные возможности `pytest`

#### a) **Использование фикстур**

В `pytest` фикстуры — это мощный инструмент для подготовки данных и ресурсов для тестов. Фикстуры могут быть как простыми, так и более сложными, с применением зависимостей.

Пример фикстуры:

```python
import pytest

@pytest.fixture
def sample_data():
    return {"key": "value"}

def test_dict(sample_data):
    assert sample_data["key"] == "value"
```

#### b) **Параметризация тестов**

С помощью декоратора `pytest.mark.parametrize` можно запускать один и тот же тест с разными входными данными. Это позволяет значительно сократить количество кода при тестировании различных комбинаций значений.

Пример:

```python
import pytest

@pytest.mark.parametrize("x, y, expected", [
    (2, 3, 5),
    (1, 1, 2),
    (10, 20, 30)
])
def test_add(x, y, expected):
    assert x + y == expected
```

#### c) **Асинхронные тесты с `pytest`**

Для тестирования асинхронных функций в `pytest` можно использовать библиотеку `pytest-asyncio`, которая позволяет тестировать асинхронные коды без использования специального API.

Пример:

```python
import pytest
import asyncio

@pytest.mark.asyncio
async def test_async_function():
    result = await asyncio.sleep(1, result=20)
    assert result == 20
```

#### d) **Проверка исключений**

В `pytest` можно использовать `pytest.raises` для проверки того, что в ходе выполнения кода выбрасывается ожидаемое исключение.

Пример:

```python
import pytest

def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b

def test_divide_zero():
    with pytest.raises(ValueError):
        divide(10, 0)
```

#### e) **Тестирование с помощью `pytest` и мокирования**

В `pytest` можно использовать `unittest.mock` или внешнюю библиотеку `pytest-mock` для мокирования зависимостей.

Пример:

```python
import pytest
from unittest.mock import MagicMock

def fetch_data(api_client):
    return api_client.get_data()

def test_fetch_data():
    mock_api = MagicMock()
    mock_api.get_data.return_value = "mocked data"
    assert fetch_data(mock_api) == "mocked data"
```

#### f) **Тестирование с использованием таймаутов**

В `pytest` можно указать таймаут для тестов с помощью параметра `timeout`. Это полезно, когда вы хотите ограничить время выполнения асинхронных или долгих операций.

Пример:

```python
import pytest
import asyncio

@pytest.mark.timeout(1)  # таймаут в 1 секунду
async def test_long_running_task():
    await asyncio.sleep(2)  # этот тест завершится с ошибкой из-за превышения таймаута
```

---

### Заключение

И `unittest`, и `pytest` предлагают множество дополнительных возможностей для более гибкого и продвинутого тестирования кода:

- Мокирование и фикстуры для изоляции тестов и подготовки данных.
- Параметризация тестов и проверка исключений для улучшения покрытия тестами.
- Работа с асинхронным кодом и установка таймаутов для долгих операций.

Эти возможности позволяют тестировать более сложные сценарии, улучшая качество кода и облегчая его поддержку.