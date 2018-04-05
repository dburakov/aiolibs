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

- HTTP API
- Worker

---

### A new service TODO:

- 

---

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

Presentation: https://gitpitch.com/dburakov/aiolibs

Libs: https://github.com/wgnet?q=aio

Links:

- https://blog.codinghorror.com/the-infinite-space-between-words/
- https://hackernoon.com/yes-python-is-slow-and-i-dont-care-13763980b5a1

