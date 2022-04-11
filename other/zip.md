Удобная работа архивами `zip`
---
При работе приложения может потребоваться работать с архивами, для этого можно 
использовать модуль `zip` самого `python`

Представим себе ситуацию, что нам требуется создать `zip` архив и сохранить его в 
модели, сохранять в архив будем список из файлов.

```python
import os, tempfile
import zipfile

name = 'Название ZIP архива'

files = ['Файл.docx', 'Файл.xlsx', 'Файл.txt']

with tempfile.TemporaryDirectory() as tmpdir:
    
    # создание имени и пути для zip архива
    zip_name = r'%s.zip' % name
    zip_path = os.path.join(tmpdir, zip_name)
    
    # создаем zip архив
    new_zip = zipfile.ZipFile(zip_path, 'w')
    
    # пакуем все файлы в zip архив
    for path in files:
        new_zip.write(
            
            # сохраняем файл в zip архив
            filename=path, 
            
            # когда файл сохраняется в zip архив он может быть сохранен со всей 
            # вложенной структурой каталогов, тут мы указываем путь с названием для
            # сохранения структуры, сохранить в корень с названием файла.
            arcname=os.path.basename(path) 
        )
    
    # закрываем zip архив
    new_zip.close()
```

Сохраняем zip архив в объекте модели

```python
self.zip_reports.save(
    content=File(open(zip_path, "rb")),
    name=zip_name,
    save=False
)
```
