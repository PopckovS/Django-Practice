Трансляция
---
Установка настроек для перевода  

```python
from django.utils.translation import gettext_lazy as _

LANGUAGES = (
    ('ru', _('Russian')),
    ('en', _('English'))
)
```