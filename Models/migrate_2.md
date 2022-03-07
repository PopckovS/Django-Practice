Проблемы при создании миграций
---

Когда мы создаем 2 модели сразу, первичную и вторичную, и в них еще
нет записей то все окей, но в случае если мы уже создали вторичную
таблицу `Women` и внесли в нее записи, и только после этого создаем 
первичную таблицу, и при этом не задаем `null=True` в поле
которое отвечает за взаимосвязь, то получается что у нас есть записи
в которых нет ссылки на родительскую категорию, и нет возможности
заполнить это поле `NULL` значением которое мы могли бы туда 
вставить, возникает конфуз.

К примеру эти 2 таблицы:
```python
from django.db import models
from django.urls import reverse


class category(models.Model):
    name = models.CharField(max_length=100, db_index=True)

class Women(models.Model):
    cat = models.ForeignKey('category', on_delete=models.PROTECT)
    title = models.CharField(max_length=255)
    content = models.TextField(blank=True)
```

В случае если пытаемся создать миграции 
`python3 manage.py makemigrations` тогда `Django` спросит что ему 
делать в такой ситуации:  

```python
Please select a fix:
 1) Provide a one-off default now 
    (will be set on all existing rows with a null value for this column)
 2) Quit, and let me add a default in models.py
Select an option: 

```

Так `Django` предупреждает об ошибке, для того чтобы это изменить, 
указать что делать с непустыми записями, добавим `null=True` и тогда
все записи что уже существуют нол не имеют ссылку на `первичную` таблицу
получат значение `null`

```python
cat = models.ForeignKey('category', on_delete=models.PROTECT, null=True)
```

---
Ошибка при развороте миграций `"django_content_type" does not exist`
---
Во время первого развороте проекта, при запуске миграций в проекте возникла ошибка
такого вида:

    python3 manage.py migrate --settings=config.settings.local

    # Ошибка
    ...
    django.db.utils.ProgrammingError: relation "django_content_type" does not exist
    LINE 1: ..."."app_label", "django_content_type"."model" FROM "django_co...

Возможная причина
>Дело в том что при запуске миграций происходит и запуск программного кода, и 
>если на этом этапе происходит обращение к БД, к таблицам что еще не существуют,
>то и может возникнуть эта ошибка.

Один из способов решения проблемы это обработка исключений, `try` `except`
просто проигнорировать данную проблему.

Второй возможный вариант, происходит в случае если где-то в коде происходит 
функции или метода который выбирает данные из БД, к примеру так: 

```python
class GroupAdminForm(forms.ModelForm):
    permissions = forms.ModelMultipleChoiceField(
        queryset=UserAuth.objects.all(),
        widget=widgets.FilteredSelectMultiple(_('permissions'), False)
    )
```

В данном случае мы делаем выборку данных на прямую указывая функцию, и в 
этом случае функция тут же исполняется, ибо функция это тоже объект, и 
исполниться в момент вызова.

Мы можем решить это, указав только название функции как коллбэк.

```python
def get_qs_group_admin():
    return forms.ModelMultipleChoiceField(
            queryset=UserAuth.objects.all(),
            widget=widgets.FilteredSelectMultiple(_('permissions'), False)
        )
    
class GroupAdminForm(forms.ModelForm):
    permissions = get_qs_group_admin
```

Мы не вызываем функцию на исполнение сразу, но указываем ее класс, а 
сама функция будет вызвана как коллбэк.
