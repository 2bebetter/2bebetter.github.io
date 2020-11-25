---
layout: post
title: "在Django项目中使用Celery+Rabbitmq"
date:  2020-11-20 12:51:40 +0800
typora-root-url: ..
categories: gitlab
---

* content
{:toc}

在Django项目中使用celery+rabbitmq。

## background

### celery

Celery 是一款非常简单、灵活、可靠的分布式系统，可用于处理大量消息，并且提供了一整套操作此系统的一系列工具。

Celery 是一款消息队列工具，可用于处理实时数据以及任务调度。

### rabbitmq

**RabbitMQ**是实现了高级消息队列协议（AMQP）的开源消息代理软件（亦称面向消息的中间件）。

RabbitMQ服务器是用[Erlang](https://baike.baidu.com/item/Erlang)语言编写的，而集群和故障转移是构建在[开放电信平台](https://baike.baidu.com/item/开放电信平台)框架上的。所有主要的[编程语言](https://baike.baidu.com/item/编程语言/9845131)均有与代理接口通讯的[客户端](https://baike.baidu.com/item/客户端/101081)库。

### virtualenv

VirtualEnv用于在一台机器上创建多个独立的python运行环境。VirtualEnvWrapper为前者提供了一些便利的命令行上的封装。

## Run

### Using virtualenv

```bash
virtualenv dashboardenv --python=3.5
source dashboardenv/bin/activate
pip3 install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```

### Using rabbitmq with celery

#### install rabbitmq

- 提前安装erlang=23.0.3（https://blog.csdn.net/s_lisheng/article/details/79529113）
- 安装3.8.6的rabbitmq(https://blog.csdn.net/yanxilou/article/details/104467756/)

#### configure rabbitmq

```bash
sudo service rabbitmq-server     start
sudo rabbitmq-plugins enable     rabbitmq_management
sudo rabbitmqctl add_user     username password
sudo rabbitmqctl     set_user_tags username administrator
sudo rabbitmqctl add_vhost     sysname
sudo rabbitmqctl     set_permissions -p sysname username      ".*" ".*" ".*" 
```

### Using celery in django

在django项目中直接使用celery

```python
from __future__ import absolute_import, unicode_literals
import os
from celery import Celery
from kombu import Exchange, Queue

# set the default Django settings module for the 'celery' program.
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'dashboard.settings')

app = Celery(
    'dashboard',
    broker='pyamqp://username:passsword@127.0.0.1/sysname',
    backend='rpc://username:passsword@127.0.0.1/sysname'
)
# Note that any configuration that was previously set will be reset when config_from_object() is called.
# If you want to set additional configuration you should do so after.
app.config_from_object('django.conf:settings', namespace='CELERY_NODE')

# set queue
default_exchange = Exchange('default', type='topic')

app.conf.task_queues = (
    Queue('default', default_exchange, routing_key='default.#' ),
    Queue('crnodesys', crnodesys_exchange, routing_key='period.#'),
)
# set default queue and exchange
app.conf.update(
    CELERY_IGNORE_RESULT = True,
    CELERY_STORE_ERRORS_EVEN_IF_IGNORED = False,
	task_default_queue = 'default',
	task_default_exchange = 'default',
	task_default_exchange_type = 'topic',
	task_default_routing_key = 'default.task'
)
# set routings
app.conf.task_routes = ([
    ('tasks.*',              {'queue': 'period', 'routing_key': 'period.task'}),
],)
# Load task modules from all registered Django app configs.
app.autodiscover_tasks()

@app.task(bind=True)
def debug_task(self):
    print('Request: {0!r}'.format(self.request))
```

### Run

* run django

  ```bash
  python ./manage.py runserver localhost:8000
  ```

* run celery

  ```bash
  celery worker -A dashboard -l info
  ```