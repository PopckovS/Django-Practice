Флеш сообщения
---

[Документация сообщений](https://djangodoc.ru/3.1/ref/contrib/messages/)

Обычно используются для отображения сообщения после обработки формы 
или некоторого действия пользователя.

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

Существует несколько способов вызывать сообщения разных уровней и передавать
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
