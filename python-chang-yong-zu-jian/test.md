# 定时任务-APScheduler

## APScheduler定时任务工具

### 特点

* 不依赖于Linux系统的crontab系统定时，独立运行
* 可以**动态添加**新的定时任务，如下单后30分钟内必须支付，否则取消订单，就可以借助此工具（每下一单就要添加此订单的定时任务）
* 对添加的定时任务可以做持久保存

### 安装

```bash
pip install apscheduler
```

### 组成

APScheduler由以下四部分组成:

* **Triggers** 触发器 指定定时任务执行的时机
* **Job stores** 存储器 可以将定时持久存储
* **Executors** 执行器 在定时任务该执行时，以进程或线程方式执行任务
* **Schedulers** 调度器 常用的有BackgroundScheduler（**后台运行**）和BlockingScheduler\(**阻塞式**\)

### 使用方式

```python
from apscheduler.schedulers.background import BlockingScheduler

# 创建定时任务的调度器对象
scheduler = BlockingScheduler()
# 创建执行器
executors = {
    'default': ThreadPoolExecutor(20),
}
# 定义定时任务
def my_job(param1, param2):  # 参数通过add_job()args传递传递过来
    print(param1)  # 100
    print(param2)  # python

# 向调度器中添加定时任务
scheduler.add_job(my_job, 'date', args=[100, 'python'], executor=executors)

# 启动定时任务调度器工作
scheduler.start()
```

### 调度器Scheduler

负责管理定时任务

* `BlockingScheduler`: 作为独立进程时使用

```python
from apscheduler.schedulers.blocking import BlockingScheduler

scheduler = BlockingScheduler()
scheduler.start()  # 此处程序会发生阻塞
```

* `BackgroundScheduler`: 在框架程序（如Django、Flask）中使用

```python
from apscheduler.schedulers.background import BackgroundScheduler

scheduler = BackgroundScheduler()
scheduler.start()  # 此处程序不会发生阻塞
```

### 执行器Executors

在定时任务该执行时,以进程或线程方式执行任务

* `ThreadPoolExecutor 线程方式`

```python
from apscheduler.executors.pool import ThreadPoolExecutor
ThreadPoolExecutor(max_workers)
```

使用方法

```python
from apscheduler.executors.pool import ThreadPoolExecutor

executors = {
      'default': ThreadPoolExecutor(20) # 最多20个线程同时执行
  }
  scheduler = BackgroundScheduler(executors=executors)
```

* `ProcessPoolExecutor 进程方式`

```python
from apscheduler.executors.pool import ProcessPoolExecutor
ProcessPoolExecutor(max_workers)
```

使用方法

```python
from apscheduler.executors.pool import ProcessPoolExecutor

executors = {
      'default': ProcessPoolExecutor(5) # 最多5个进程同时执行
  }

scheduler = BackgroundScheduler(executors=executors)
```

### 触发器Trigger

指定定时任务执行的时机

* **date在特定时间日期执行**

```python
from datetime import date

# 在2019年11月6日00:00:00执行
sched.add_job(my_job, 'date', run_date=date(2019, 11, 6))

# 在2019年11月6日16:30:05
sched.add_job(my_job, 'date', run_date=datetime(2009, 11, 6, 16, 30, 5))
sched.add_job(my_job, 'date', run_date='2009-11-06 16:30:05')

# 立即执行
sched.add_job(my_job, 'date')  
sched.start()
```

* **interval经过指定的时间间隔执行**
  * **weeks** \(int\) – number of weeks to wait
  * **days** \(int\) – number of days to wait
  * **hours** \(int\) – number of hours to wait
  * **minutes** \(int\) – number of minutes to wait
  * **seconds** \(int\) – number of seconds to wait
  * **start\_date** \(datetime\|str\) – starting point for the interval calculation
  * **end\_date** \(datetime\|str\) – latest possible date/time to trigger on
  * **timezone** \(datetime.tzinfo\|str\) – time zone to use for the date/time calculations

```python
from datetime import datetime

# 每两小时执行一次
sched.add_job(job_function, 'interval', hours=2)

# 在2012年10月10日09:30:00 到2014年6月15日11:00:00的时间内，每两小时执行一次
sched.add_job(job_function, 'interval', hours=2, start_date='2012-10-10 09:30:00', end_date='2014-06-15 11:00:00')
```

* **cron按指定的周期执行**
  * **year** \(int\|str\) – 4-digit year
  * **month** \(int\|str\) – month \(1-12\)
  * **day** \(int\|str\) – day of the \(1-31\)
  * **week** \(int\|str\) – ISO week \(1-53\)
  * **day\_of\_week** \(int\|str\) – number or name of weekday \(0-6 or mon,tue,wed,thu,fri,sat,sun\)
  * **hour** \(int\|str\) – hour \(0-23\)
  * **minute** \(int\|str\) – minute \(0-59\)
  * **second** \(int\|str\) – second \(0-59\)
  * **start\_date** \(datetime\|str\) – earliest possible date/time to trigger on \(inclusive\)
  * **end\_date** \(datetime\|str\) – latest possible date/time to trigger on \(inclusive\)
  * **timezone** \(datetime.tzinfo\|str\) – time zone to use for the date/time calculations \(defaults to scheduler timezone\)

```python
# 在6、7、8、11、12月的第三个周五的00:00, 01:00, 02:00和03:00 执行
sched.add_job(job_function, 'cron', month='6-8,11-12', day='3rd fri', hour='0-3')

# 在2014年5月30日前的周一到周五的5:30执行
sched.add_job(job_function, 'cron', day_of_week='mon-fri', hour=5, minute=30, end_date='2014-05-30')
```

### 任务存储

* **MemoryJobStore 默认内存存储**
* **MongoDBJobStore 任务保存到MongoDB**

```python
from apscheduler.jobstores.mongodb import MongoDB

JobStoreMongoDBJobStore()
```

* **RedisJobStore 任务保存到redis**

```python
from apscheduler.jobstores.redis import RedisJobStore
RedisJobStore()
```

### 任务存储使用方法\(待定\)

### 启动

```python
scheduler.start()
```

* 对于BlockingScheduler ，程序会阻塞在这，防止退出，作为独立进程时使用。（可以用来生成静态页面）
* 对于BackgroundScheduler，可以在应用程序中使用。不再以单独的进程使用。（如30分钟内取消订单）

### 任务管理

#### 方式1

```python
job = scheduler.add_job(myfunc, 'interval', minutes=2)  # 添加任务
job.remove()  # 删除任务
job.pause() # 暂定任务
job.resume()  # 恢复任务
```

#### 方式2

```python
scheduler.add_job(myfunc, 'interval', minutes=2, id='my_job_id')  # 添加任务    
scheduler.remove_job('my_job_id')  # 删除任务
scheduler.pause_job('my_job_id')  # 暂定任务
scheduler.resume_job('my_job_id')  # 恢复任务
```

### 调整任务调度周期

```python
job.modify(max_instances=6, name='Alternate name')

scheduler.reschedule_job('my_job_id', trigger='cron', minute='*/5')
```

### 停止APScheduler运行

```python
scheduler.shutdown()
```

