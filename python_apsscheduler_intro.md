---
title: "Scheduling Your Tasks with Package Apscheduler"
date: 2021-10-20T01:05:36+08:00
draft: false
tags: []
categories: [Python]
---

In Python, to run a task periodically, we can use the package [apscheduler](https://github.com/agronholm/apscheduler).

<!--more-->

Two schedulers are provided in this package, [BackgroundScheduler](https://apscheduler.readthedocs.io/en/stable/modules/schedulers/background.html#module-apscheduler.schedulers.background) and [BlockingScheduler](https://apscheduler.readthedocs.io/en/stable/modules/schedulers/blocking.html#module-apscheduler.schedulers.blocking). BackgroundScheduler will run in the background in a non-blocking fashion. On the other hand, BlockingScheduler will block until the job assigned is finished.

Here is some demo code to showcase their usage:

```python
from datetime import datetime

from apscheduler.schedulers.background import BackgroundScheduler, BlockingScheduler

scheduler = BlockingScheduler()
scheduler.add_job(func=my_job, trigger='interval', seconds=2, id='my custom task')
scheduler.start()


def my_job():
    print(f"custom job called at {datetime.now()}")
```

In the above code, `my_job` is the task we want to run. We use the [`interval` trigger](https://apscheduler.readthedocs.io/en/stable/modules/triggers/interval.html#module-apscheduler.triggers.interval). Trigger defines when we will run the given job. By using interval trigger, the job provided will be run periodically at the specified interval. For example, in the above code, the job will be run every two seconds, and we will observe output like the following:

```
custom job called at 2021-01-16 17:25:45.701729
custom job called at 2021-01-16 17:25:47.701612
custom job called at 2021-01-16 17:25:49.701627
custom job called at 2021-01-16 17:25:51.701584
```

We can use [`print_jobs()`](https://apscheduler.readthedocs.io/en/stable/modules/schedulers/base.html#apscheduler.schedulers.base.BaseScheduler.print_jobs) to print job info for this scheduler.

There is another [`cron` trigger](https://apscheduler.readthedocs.io/en/stable/modules/triggers/cron.html#module-apscheduler.triggers.cron). Cron trigger will run the job if the given constraints is met. Here is an example of using the cron trigger:

```python
from datetime import datetime

from apscheduler.schedulers.background import BackgroundScheduler, BlockingScheduler

scheduler = BlockingScheduler()
scheduler.add_job(func=my_job, trigger='cron', second=30, id='my custom task')
scheduler.start()


def my_job():
    print(f"custom job called at {datetime.now()}")
```

In the above code, the job will run on every year, every month, every day,
every hour, every minute, on the 30th second. An example output is like the
following:

```
custom job called at 2021-01-16 17:39:30.001336
custom job called at 2021-01-16 17:40:30.001498
custom job called at 2021-01-16 17:41:30.001821
custom job called at 2021-01-16 17:42:30.001922
```

Another example:

```python
scheduler.add_job(func=my_job, trigger='cron', minute=0, second=30, id='my custom task')
```

In the above code, the job will run on every year, every month, every day,
every hour, on minute 0 and second 30. For example, it will run on `17:00:30`,
`18:00:30` etc. Output will be like the following:

```
custom job called at 2021-01-17 21:00:30.001602
custom job called at 2021-01-17 22:00:30.001254
custom job called at 2021-01-17 23:00:30.001562
custom job called at 2021-01-18 00:00:30.001559
custom job called at 2021-01-18 01:00:30.001399
```
# Ref

+ apscheduler documentation: https://apscheduler.readthedocs.io/en/3.x/
