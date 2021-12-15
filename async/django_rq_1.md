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
