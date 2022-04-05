Задачи и очереди `rq`, `django-rq`
---
---

[Пакет rq](https://github.com/rq/rq)

[Пакет django-rq](https://github.com/rq/django-rq)

`RQ` (Redis Queue) - это библиотека самого `python` для поставновки задач в очередь
и их обработки в фоновом режиме с помощью джобов, интегрируется с `Redis`   

`Django-rq` - это интеграция пакета 'rq' в среду `Django`, с настройкой очередей в
`settings.py`

---
Функции коллбэки `on_success`, `on_failure`
---
При запуске очереди на выполнение джобом с помощью `django-rq`, можно указывать
функции коллбэки на выполнение при удачном и не удачном завершении задачи.

```python
from django_rq import enqueue

def on_success_job_callback(job, connection, result):
    logger.info('Задача %s выполнена успешно', job.id)
    ...

def on_failure_job_callback(job, connection, *args, **kwargs):
    logger.error('Задача %s не выполнена', job.id)
    ...
    
enqueue(func_for_async, param_1, param_2,     
    on_success=on_success_job_callback, 
    on_failure=on_failure_job_callback
)
```

---
Отладка асинхронного кода `django-rq`
---

Ситуация, у нас есть основная программаи ряд мест где происходит запус 
асинхронного кода, то есть запуск `job` для того что бы отладить такой
код нам требуется либо на момент отладки отключить асинхронность и вызывать
код синхронно.

Мы можем отклчюить выполнение асинхронного кода для `django-rq` вабще. 

Настройки для `django-rq`

```python
RQ_QUEUES = {
    'default': {
        'HOST': env.str('REDIS_HOST_BROKER'),
        'PORT': env.int('REDIS_PORT_BROKER'),
        'DB': env.int('REDIS_DB_BROKER'),
        'DEFAULT_TIMEOUT': env.int('RQ_DEFAULT_TIMEOUT', default=300),
    }
}
```

Отключаем асинхронность для `django-rq`
```python
RQ_QUEUES = locals().setdefault('RQ_QUEUES', dict())

for queueConfig in RQ_QUEUES.values():
    queueConfig['ASYNC'] = False
```

---
---
---
---
---


    pip3 install django-rq
    pip3 install django-rq-scheduler


Запускаем воркер в отдельном терминале, запускаем с настройками `default`,

    python manage.py rqworker default

Если был пароль не пуст в настройках `default` может возникнуть проблема : 

    Client sent AUTH, but no password is set







