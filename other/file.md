---
Отдать файл на скачивание `Django`
---
В Django есть специальный модуль для этой цели, отдаем пользователю
на скачивание файл по указанному пути, 

```python
from django.http import FileResponse

# отдаем открытый дескриптор файла с модификацией
# на чтение в бинарной форме.
return FileResponse(open(path, 'rb'))
```
