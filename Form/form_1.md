Формы отправки
---

Создание форм происходит схожим образом, как и описание моделей, каждое
поле модели определяется своим классом, также и каждое поле формы 
определяется своим классом. Базовым классом для формы является
класс `django.forms.Form` через него и создаем класс, который будет
описывать форму отправки.

```python
from django import forms

class NameForm(forms.Form):
    name = forms.CharField(label='name', max_length=100)
```

---
Методы Форм
---
`is_valid()` - результат проверки, валидны ли данные пришедшие из формы.   



---
Поля формы
---

Классы для описания полей формы

`CharField`


---
Параметры для полей формы
---

Некоторые из этих параметров создаю проверку формы, у экземпляра
класса формы есть специальный метод `is_valid()` который будет
производить проверку введенных данных.

`widget` - виджет которые будет отображать это поле на форме.

`initial` - начальное значение для поля, его тип зависит от типа 
самого поля.

`label` - str: текст над полем

`max_length` - int: максимальное количество символов для ввода в поле

`required` - boolean: является ли поле обязательным к заполнению 


---
Примеры реализации форм
---

**Пример №1**
```python
import datetime
from django import forms
from django.forms import widgets

class MyFormForModel(forms.ModelForm):
    """Создаем форму для модели."""

    datetime_from = forms.DateField(
        label='Начало даты',
        required=True,
        widget=widgets.DateInput(attrs={'class': 'vDateField'}),
        initial=datetime.datetime.now() - datetime.timedelta(days=1),
    )

    datetime_to = forms.DateField(
        label='Конец даты',
        required=True,
        widget=widgets.DateInput(attrs={'class': 'vDateField'}),
        initial=datetime.datetime.now(),
    )

    def __init__(self, *args, **kwargs):
        super(MyFormForModel, self).__init__(*args, **kwargs)
        
    class Meta:
        """Описание мета информации о классе"""
        model = MyModel
        fields = '__all__'

```
