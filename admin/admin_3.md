Флеш сообщения
---

[Документация сообщений](https://djangodoc.ru/3.1/ref/contrib/messages/)

Обычно используются для отображения сообщения после обработки формы 
или некотрого дейстчвия пользователя.

Для работы должны быть следующие настройки:
```python
DJANGO_APPS = [
    'django.contrib.messages',
]

MIDDLEWARE = [
    'django.contrib.messages.middleware.MessageMiddleware',
]
TEMPLATES = [
    {
        'OPTIONS': {
            'context_processors': [
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

Существует несколько способов вызывать мсообщения разных уровне и передавать
в них сообщения, они действуют одинаково

```python
# №1
messages.add_message(request, messages.SUCCESS, 'Успех')

# №2
messages.success(request, 'Profile details updated.')
```

Создадим кнопки для сообщений разных уровней и привяжем их к методам 
обработчикам, 

```python
from django.contrib import messages
from django.http import HttpResponseRedirect
from django.urls import reverse
from django.conf.urls import url

    def get_urls(self):
        """Добавляем путь к кнопкам"""
        urls = super().get_urls()
        custom_urls = [
            url('^messages_success/$', self.messages_success, name='messages_1'),
            url('^messages_warning/$', self.messages_warning, name='messages_2'),
            url('^messages_debug/$', self.messages_warning, name='messages_3'),
            url('^messages_info/$', self.messages_info, name='messages_4'),
            url('^messages_error/$', self.messages_error, name='messages_5'),
        ]
        return custom_urls + urls

    # from django.contrib.auth import authenticate


    def messages_success(self, request):
        messages.success(request, 'Profile details updated.')
        return HttpResponseRedirect(reverse('admin:test1_messagemodel_changelist'))

    def messages_warning(self, request):
        messages.warning(request, 'Your account expires in three days.')
        return HttpResponseRedirect(reverse('admin:test1_messagemodel_changelist'))

    def messages_debug(self, request):
        messages.debug(request, 'SQL statements were executed.')
        return HttpResponseRedirect(reverse('admin:test1_messagemodel_changelist'))

    def messages_info(self, request):
        messages.info(request, 'Three credits remain in your account.')
        return HttpResponseRedirect(reverse('admin:test1_messagemodel_changelist'))

    def messages_error(self, request):
        messages.error(request, 'Document deleted.')
        return HttpResponseRedirect(reverse('admin:test1_messagemodel_changelist'))
```

Переопределим шаблон
```html
      <form action="messages_success/" method="POST">
        {% csrf_token %}
        <p>messages_success</p>
        <input type="submit" value="messages_success" />
      </form>
```

---
Логирование
---

[Документация логирования](https://djangodoc.ru/3.2/topics/logging/)

У самого языка `Python` есть свой собственный встроенный модуль для
логирования `logging`

Пример работы
```python
import logging

logger = logging.getLogger('logger_for_debug')

def some_function():
    if False:
        logger.error('Ошибка ...')
```

`logging.getLogger()` - получает или если не существовало ранее, создает 
экземпляр регистратора для логирования сообшений, имя регистратора будет 
его уникальным идентификатором.

Виды логирования:

- `logger.debug()`
- `logger.info()`
- `logger.warning()`
- `logger.error()`
- `logger.critical()`

---
Ведение журнала для логирования.

Настроить журнал ведения логирования можно по разному, чаще используют
способ через словарь `LOGGING`. 

`LOGGING` - словарь с параметрами который будет описывать как вести журнал
логирования, фильтры и средства форматирования для ведения логов.

Настройки `settings.py` с параметрами `LOGGING` для ведения журнала:
```python
import os

LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
        },
    },
    'root': {
        'handlers': ['console'],
        'level': 'WARNING',
    },
    'loggers': {
        'django': {
            'handlers': ['console'],
            'level': os.getenv('DJANGO_LOG_LEVEL', 'INFO'),
            'propagate': False,
        },
    },
}
```

