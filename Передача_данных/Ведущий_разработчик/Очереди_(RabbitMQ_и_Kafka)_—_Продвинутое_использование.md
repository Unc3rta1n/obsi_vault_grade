1. RabbitMQ — Продвинутое использование

**RabbitMQ** — это брокер сообщений, который использует модель очередей для передачи сообщений между приложениями. Он реализует протокол AMQP (Advanced Message Queuing Protocol), который позволяет приложению отправлять сообщения, а получатели (потребители) извлекают их в нужном порядке.

#### Преимущества RabbitMQ:
- **Надежность**: RabbitMQ гарантирует доставку сообщений, поддерживает подтверждения и повторную доставку.
- **Поддержка различных типов обмена**: RabbitMQ поддерживает различные модели обмена сообщениями, такие как очередь (queue), публикация-подписка (pub/sub) и маршрутизация (routing).
- **Масштабируемость**: RabbitMQ может быть масштабирован горизонтально для обработки большого объема сообщений и нагрузки.

#### Продвинутое использование RabbitMQ:

2. **Работа с несколькими очередями и обменниками**: RabbitMQ позволяет создавать несколько очередей и связывать их с различными обменниками для маршрутизации сообщений.

Пример с использованием нескольких очередей и обменников:

```python
import pika

# Создаем подключение и канал
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

# Создаем два обменника
channel.exchange_declare(exchange='logs', exchange_type='fanout')
channel.exchange_declare(exchange='direct_logs', exchange_type='direct')

# Создаем две очереди
channel.queue_declare(queue='queue_1')
channel.queue_declare(queue='queue_2')

# Привязываем очереди к обменникам
channel.queue_bind(exchange='logs', queue='queue_1')
channel.queue_bind(exchange='direct_logs', queue='queue_2', routing_key='error')

# Отправка сообщений
channel.basic_publish(exchange='logs', routing_key='', body='Message for logs')
channel.basic_publish(exchange='direct_logs', routing_key='error', body='Error message')

connection.close()
```

3. **Подтверждения сообщений**: Для надежности RabbitMQ использует подтверждения для сообщений, что позволяет гарантировать, что сообщения не будут потеряны.

Пример с подтверждениями:

```python
import pika

def on_delivery_confirmation(frame):
    if frame.method.NAME == 'Basic.Ack':
        print("Message confirmed")
    elif frame.method.NAME == 'Basic.Nack':
        print("Message not confirmed")

# Создаем подключение и канал
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

# Включаем подтверждения
channel.confirm_select()
channel.add_on_delivery_confirmation(on_delivery_confirmation)

# Отправка сообщения
channel.basic_publish(exchange='logs', routing_key='', body='Message for logs')

connection.close()
```

4. **Отложенные сообщения**: Для отложенной доставки сообщений можно использовать плагин `rabbitmq-delayed-message-exchange`.

### 2. Apache Kafka — Продвинутое использование

**Apache Kafka** — это распределенная система для обработки потоков данных и брокер сообщений, ориентированная на производительность и масштабируемость. Kafka широко используется для обработки больших объемов данных в реальном времени и является основой для многих систем аналитики и мониторинга.

#### Преимущества Kafka:
- **Высокая пропускная способность**: Kafka может обрабатывать миллионы сообщений в секунду.
- **Масштабируемость и отказоустойчивость**: Kafka легко масштабируется и предоставляет встроенную отказоустойчивость.
- **Поддержка сохранения сообщений**: Kafka позволяет сохранять сообщения на длительное время, что полезно для репликации и восстановления данных.

#### Продвинутое использование Kafka:

5. **Использование нескольких разделов (partitions)**: Kafka позволяет разделять данные на несколько разделов в рамках одного топика, что позволяет эффективно распределять нагрузку и увеличивать скорость обработки.

Пример с несколькими разделами:

```python
from kafka import KafkaProducer

# Создаем продюсера
producer = KafkaProducer(bootstrap_servers='localhost:9092')

# Отправляем сообщение в конкретный раздел
producer.send('my_topic', value=b'Message for partition 0', partition=0)
producer.send('my_topic', value=b'Message for partition 1', partition=1)

producer.flush()
```

6. **Использование ключей для маршрутизации сообщений**: Kafka использует ключи для определения, в какой раздел отправлять сообщение. Это позволяет контролировать порядок сообщений.

Пример с ключами для маршрутизации:

```python
from kafka import KafkaProducer

# Создаем продюсера
producer = KafkaProducer(bootstrap_servers='localhost:9092')

# Отправляем сообщения с разными ключами
producer.send('my_topic', key=b'key1', value=b'Message for key1')
producer.send('my_topic', key=b'key2', value=b'Message for key2')

producer.flush()
```

7. **Использование Kafka Streams для обработки потоков данных**: Kafka Streams — это библиотека для обработки потоков данных непосредственно в Kafka, что позволяет выполнять такие операции, как фильтрация, агрегация, соединение потоков и т.д.

Пример использования Kafka Streams (в Java, но принцип тот же):

```java
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream = builder.stream("my_topic");
stream.filter((key, value) -> value.contains("important"))
      .to("filtered_topic");

KafkaStreams streams = new KafkaStreams(builder.build(), config);
streams.start();
```

8. **Конфигурация репликации и отказоустойчивости**: Kafka позволяет конфигурировать количество реплик для каждого раздела, обеспечивая высокую доступность данных в случае сбоя.

Пример настройки репликации в Kafka:

```bash
bin/kafka-topics.sh --create --topic my_topic --bootstrap-server localhost:9092 --partitions 3 --replication-factor 2
```

### Заключение

- **RabbitMQ** используется для обмена сообщениями между сервисами с гарантией доставки, поддержкой различных типов маршрутизации сообщений и масштабируемостью.
- **Kafka** идеально подходит для обработки больших объемов данных и потоковых приложений, предлагая мощные инструменты для разделения данных на топики и разделы, а также обеспечения отказоустойчивости и долговременного хранения сообщений.

Обе системы предоставляют мощные инструменты для разработки распределённых приложений и могут быть использованы в различных сценариях в зависимости от требований к производительности и надежности.
