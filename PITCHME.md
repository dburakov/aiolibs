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

Types of services:

- Worker
- HTTP API

---

#### The main

[Press Down Key]

+++

```python
from time import localtime

activities = {8: 'Sleeping', 9: 'Commuting', 17: 'Working',
              18: 'Commuting', 20: 'Eating', 22: 'Resting' }

time_now = localtime()
hour = time_now.tm_hour

for activity_time in sorted(activities.keys()):
    if hour < activity_time:
        print activities[activity_time]
        break
else:
    print 'Unknown, AFK or sleeping!'
```

@[1]
@[3-4]
@[6-7]
@[9-14]

###### Use code-presenting to **step-thru** code <p> from directly within your presentation 

---

Presentation: https://gitpitch.com/dburakov/aiolibs

Libs: https://github.com/wgnet?q=aio

Links:

- https://blog.codinghorror.com/the-infinite-space-between-words/
- https://hackernoon.com/yes-python-is-slow-and-i-dont-care-13763980b5a1

