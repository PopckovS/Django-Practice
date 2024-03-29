Хранилище `git stash`
---

`git stash` - представляет собой отдельное изолированное хранилище, в 
которое можно поместить не закомиченные изменения, все что помещается в
стек `stash` может храниться только локально, на удаленном репозитории
он не предусмотрен.

Представим семе ситуацию, мы выполняем ряд изменений, но не коммитим их,
в процессе работы у нас может возникнуть необходимость как то сохранить
эти изменения, но не делать коммита, иметь возможность перейти на другую 
ветку, вернуться, и вернуть сделанные нами изменения, как раз для этого
и подходит `git stash`. Мы можем поместить сделанные нами изменения в
`stash` и вернуть их обратно при необходимости, и для этого не потребуется
делать коммита.

Каждому сохранению присваивается уникальный номер, как `hash` у коммита,
такого типа `stash@{0}`, все сохранения размещаются сверху в низ, где
сверху самое свежее изменение, оно и имеет номер `stash@{0}`, самое 
старое сохранение находится внизу с соответствующим номером ... 
`stash@{7}`

---

**Команды:**

`git stash` - Прячет изменения в хранилище.

`git stash save "Комментарий к изменениям"` - Прячет изменения в хранилище
и дает каждому сохранению комментарий.

`git stash list` - Показать все сохранения в хранилище

`git stash apply` - Берет самое свежее сохранение `stash@{0}` и применяет
его, возвращает его изменения в ту ветку в которой вы сейчас находитесь.

`git stash pop` - Делает то же самое что и `git stash apply` но удаляет 
то самое свежее изменение `stash@{0}` которое применяет к репозиторию.

`git stash clear` - Удаляет все сохранения, что есть в храниолище.

`git stash drop` - Удаляет самое свежее сохранение `stash@{0}`

`git stash drop stash@{номер}` - Удаляет указанное сохранение.

---
.gitignore
---

Чтобы игнорировать файлы системой GIT используется файл `.gitignore`

```
    // Содержимое файла .gitignore
    *.[oa]
    *~
```
 
Все файлы, что заканчивается на расширение `.o` или `.a` или все файлы 
заканчивающиеся на `~` будут проигнорированы. 

Просмотр индексированных и неиндексированных изменений
Если команды `git status` недостаточно и надо посмотреть что конкретно 
изменилось, используется команда `git diff` эта команда покажет конкретные 
отредактированные строки файлов, 

Тут у нас есть 2 варианта поведения:

1) Если мы сделали изменения в файле, но пока не проиндексировали, и хотим
   посмотреть какие конкретно строки были добавлены/удалены, то используем 
   команду.
   
```
    git diff
```

2) Если мы внесли изменения в файл и проиндексировали его, и хотим посмотреть
какие конкретно строки были добавлены/удалены и будут внесены в фиксацию,
то используем команду.
   
```
    git diff --staged
```

---
**Фиксация изменений.**

После того как мы сделали изменения и проиндексировали изменения при помощи
`git add ...` эти изменения надо зафиксировать, при помощи команды `git commit`.

Есть 2 способа как можно использовать команд для фиксации изменений:

1) git commit -m "Комментарий к фиксации изменений"
2) git commit Без ключа `-m` будет открыт редактор для записи комментария 
к фиксации, какой именно редактор ? редактор выбирается в зависимости от 
настроек, что действуют на этот проект, а именно настройка:
   
```
    git config --global core.editor "Название редактора"
```

Эта настройка устанавливает редактор что будет использоваться `vim`, `emacs`.
В результате будет открыт выбранный редактор с комментариями для справки о 
окончанию работы комментарии сами сотрутся и останется только коммит.

---
**При каждой фиксации мы записываем снимок состояний в память проекта
и можем вернуть проект к этому состоянию в любой момент.**


Пропуск области Индексирования и сразу к Фиксированию
Мы можем обойтись и без команды `git add` и сразу зафиксировать все отслеживаемые 
файлы, для этого используем следующую команду:

```
      git commit -a -m "Комментарий"
```

---
**Удаление файлов**

```
    git rm <file name>
```

Для того чтобы система git перестала работать с файлом, его нужно удалить 
из числа отслеживаемых, и зафиксировать это. Это делается командой:

```
      git rm
```

Файл также удаляется из рабочей папки, если мы просто удалим файл из рабочей 
папки то мы получим сообщение `Changed but not updated` то есть файл удален, 
но его удаление не проиндексировано.

В чем тут суть - если мы просто удалим файл командой `rm <файл>` то файл будет 
удален, но это надо будет проиндексировать командой `git add`. А если мы 
удаляем файл командой `git rm <название файла>` то файл будет и удален и 
проиндексирован для следующего коммита сразу же. 

```
    git rm --cached <file name>
```

Может возникнуть такая ситуация что нам требуется удалить файл из `git`
но, чтобы сам файл остался в папке и остался как не отслеживаемый, это можно 
сделать след командой:

```
      git rm --cached <file name>
```

Перемещение файлов
В GIT можно переименовать файлы при помощи команды `rm` 

```
      git rm <old file name> <new file name>
```
