# Booster for aiohttp

<br><br>
<div style="text-align: right">Dmitry Burakov</div>
<div style="text-align: right">2018</div>

---

## The most expensive resource

#### What is it?

+++

## Its' YOU!

+++

![Microcervices](assets/microservices-microservices-everywhere.jpg)

+++

| Operation          | Time      | Scaled time |
|--------------------|-----------|-------------|
| 1 CPU cycle        | 0.3 ns    | 1 s         |
| RAM I/O            | 120 ns    | 6 min       |
| SSD I/O            | 50-150 μs | 2-6 days    |
| HDD I/O            | 1-10 ms   | 1-12 months |
| Network: inside DC | 3 ms      | 3 months    |
| Internet: US to EU | 81 ms     | 8 years     |

---

### Types of services:

- Worker
- HTTP API

---

### aioamqp-ext

+++

### Producer

```python
from aioamqp_ext import BaseProducer

producer = BaseProducer(**{
    'url': 'amqp://localhost:5672/',
    'exchange': 'my_exchange'
})

producer.publish_message(
    payload='foo',
    routing_key='bar'
)

producer.close()
```

@[1]
@[3-6]
@[8-11]
@[13]

+++

### Consumer

```python
from aioamqp_ext import BaseConsumer

class Consumer(BaseConsumer):
    async def process_request(self, data):
        print(data)

if __name__ == "__main__":
    consumer = Consumer(**{
        'url': 'amqp://localhost:5672/',
        'exchange': 'my_exchange',
        'queue': 'my_queue',
        'routing_key': 'foo.bar'
    })
    try:
        loop = asyncio.get_event_loop()
        loop.run_until_complete(consumer.consume())
        loop.run_forever()
    except KeyboardInterrupt:
        loop.run_until_complete(consumer.close())
    finally:
        loop.close()
```

@[1]
@[3-5]
@[8-13]
@[14-21]

---

### aiosqlalchemy_miniorm

+++

### Init

```python
from sqlalchemy import MetaData, Integer, String, DateTime
from aiosqlalchemy_miniorm import RowModel, RowModelDeclarativeMeta, BaseModelManager

BaseModel = declarative_base(
    metadata=metadata, 
    cls=RowModel, 
    metaclass=RowModelDeclarativeMeta
) 

class MyEntityManager(BaseModelManager):
    async def get_with_products(self):
        return await self.get_items([(MyEntity.c.num_products > 0)])
    
class MyEntity(BaseModel):
    __tablename__ = 'my_entity'
    __model_manager_class__ = MyEntityManager

    id = Column(Integer, primary_key=True)
    name = Column(String(100), nullable=False)
    num_products = Column(Integer)
    created_at = Column(DateTime(), server_default=text('now()'), nullable=False)
```

@[1-2]
@[4-8]
@[10-12]
@[14-21]

+++

### Query

```python
objects = await MyEntity.objects.get_instances(
    [(MyEntity.c.name == 'foo')],
    order_by=['name', '-num_products']
)

num_objects = await MyEntity.objects.count([
    (MyEntity.c.name == 'foo'), (MyEntity.c.num_products > 3)
])
```
    
@[1-4]
@[6-8]

+++

### Management

```python
record = await MyEntity.objects.insert(
    name='bar',
    num_products=0,
)

await record.update(name='baz')
await record.delete()
```

+++

### Transactions

```python
async with MyEntity.objects.transaction() as my_entity_objects:
    await my_entity_objects.insert(name='bar', num_products=0)
    await my_entity_objects.delete([(MyEntity.c.name == 'foo')])
```

---

### aiohttp_baseapi

+++

```
$ pip install aiohttp_baseapi
```

+++

```
$ baseapi-start-project
```

+++

```
$ cd src/
$ pip install -r .meta/packages
```

+++

```
$ echo "DATABASE = {'host':'localhost','port':5432,'database':'test','user':'test','password':'test','minsize':1,'maxsize':10}" > ./settings_local.py
```

+++

```
$ alembic revision --autogenerate -m "init"
$ alembic upgrade head
```

+++

```
python ./main.py --port=9000
```

+++

```
http GET :9000
http POST :9000/authors <<< '{"data":{"name":"John", "surname": "Smith"}}'
http POST :9000/books <<< '{"data":{"category": "Fiction","name":"Birthday","is_available":true, "author_id": 1}}'
http GET ":9000/books?filter[name]=Birthday&include=authors"
http GET :9000/books/1
http PUT :9000/books/1 <<< '{"data":{"is_available":false}}'
http DELETE :9000/books/1
```

+++

```
├── logs
└── src
    ├── alembic.ini
    ├── apps
    │   ├── default
    │   │   ├── __init__.py
    │   │   ├── routes.py
    │   │   └── views.py
    │   ├── demo
    │   │   ├── data_providers.py
    │   │   ├── __init__.py
    │   │   ├── models.py
    │   │   ├── routes.py
    │   │   └── views.py
    │   └── __init__.py
    ├── conf
    │   ├── defaults.py
    │   ├── __init__.py
    │   └── settings.py
    ├── core
    │   ├── app.py
    │   ├── database.py
    │   ├── dispatcher.py
    │   ├── __init__.py
    │   ├── log.py
    │   ├── middlewares.py
    │   ├── routes.py
    │   └── views.py
    ├── __init__.py
    ├── main.py
    └── migrations
        ├── env.py
        ├── script.py.mako
        └── versions

```

@[4-15]
@[16-19]
@[20-28]
@[20-28]
@[31-34]

+++

```python
class Author(BaseModel):
    __tablename__ = 'authors'

    id = sa.Column(sa.Integer, primary_key=True)
    name = sa.Column(sa.String)
    surname = sa.Column(sa.String)


class Book(BaseModel):
    __tablename__ = 'books'
    __table_args__ = (
        sa.UniqueConstraint('category', 'name', name='unique_book_name_in_category'),
    )

    id = sa.Column(sa.Integer, primary_key=True)
    category = sa.Column(sa.String(100), nullable=False, index=True)
    name = sa.Column(sa.String(255), nullable=True)
    is_available = sa.Column(sa.Boolean)
    author_id = sa.Column(sa.Integer, sa.ForeignKey(Author.id, onupdate='CASCADE', ondelete='CASCADE'), index=True)
```

---

### aiohttp_prometheus_monitoring

+++

![monitoring](assets/monitoring_types.png)

+++

### Configuring

```
MONITORING = {
    'route_ping': '/ping',
    'route_metrics': '/metrics',

    'metrics': [
        {
            'name': 'monitoring_http',
            'description': 'Check HTTP status',
            'module': 'aiohttp_prometheus_monitoring.metrics.http.HttpMetric',
            'sleep_time': 300,
            'params': {
                'url': 'http://localhost/my_ping/',
                'timeout': 1,
                'verify_ssl': True,
            }
        },
        {
            'name': 'monitoring_redis',
            'description': 'Check redis connection',
            'module': 'aiohttp_prometheus_monitoring.metrics.redis.RedisMetric',
            'sleep_time': 60,
            'params': {
                'host': 'localhost',
                'port': '6379',
            }
        },
        {
            'name': 'monitoring_mq',
            'description': 'Check MQ connection',
            'module': 'aiohttp_prometheus_monitoring.metrics.amqp.AmqpMetric',
            'sleep_time': 60,
            'params': {
                'host': 'localhost',
                'port': '5672',
                'user': 'root',
                'password': '123',
                'vhost': 'myvhost',
            }
        },
        {
            'name': 'monitoring_postgres',
            'description': 'Check postgres connection',
            'module': 'aiohttp_prometheus_monitoring.metrics.postgres.PostgresMetric',
            'sleep_time': 60,
            'params': {
                'database': 'core',
                'user': 'core',
                'password': 'core',
                'host': 'localhost',
                'port': 5433,
            }
        },
    ]
}
```

@[2-3]
@[6-16]
@[17-26]
@[27-39]
@[40-52]

+++

### Install

```
pip install aiohttp_prometheus_monitoring[amqp,redis,postgres]
```

+++

### Setup

```python
from aiohttp import web
from aiohttp_prometheus_monitoring import setup_monitoring

def create_app(loop=None):    
    app = web.Application()
    loop.run_until_complete(setup_monitoring(settings.MONITORING, app))
    
    return app
```

+++

### /metrics

```
# HELP monitoring_http Check HTTP status
# TYPE monitoring_http gauge
monitoring_http 1.0
# HELP monitoring_redis Check redis connection
# TYPE monitoring_redis gauge
monitoring_redis 1.0
# HELP monitoring_mq Check MQ connection
# TYPE monitoring_mq gauge
monitoring_mq 1.0
# HELP monitoring_postgres Check postgres connection
# TYPE monitoring_postgres gauge
monitoring_postgres 1.0
```

+++

### grafana

![graphs](assets/aiohttp_prometheus_monitoring_graphs.png)

---

Presentation: https://gitpitch.com/dburakov/aiolibs

Libs: https://github.com/wgnet?q=aio

Links:

- https://blog.codinghorror.com/the-infinite-space-between-words/
- https://hackernoon.com/yes-python-is-slow-and-i-dont-care-13763980b5a1

