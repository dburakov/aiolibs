## Optimize your most expensive resource
### Booster for aiohttp



<div style="text-align: right">Dmitry Burakov</div>

---

## The most expensive resource

#### What is it?

+++

## Its' YOU!

+++

![Microcervices](assets/microservices-microservices-everywhere.jpg)

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


