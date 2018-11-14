Git.
====
Основные команды

Commit
------

    $ git commit -a -m message - коммитит измененные файлы

Branches (создание, удаление, слияние)
--------------------------------------

### Создание новой ветки

    $ git branch testing - создает новый указатель на текущий коммит  
    (HEAD продолжает указывать на последнюю текущую ветку)
    
### Переключение веток

    $ git checkout testing - перемещает HEAD на ветку testing

Переключение веток меняет файлы в рабочей директории.

### Простое переключение веток и слияние

    $ git checkout -b iss53 -- создать новую ветку iss53 и переключиться на нее
    $                       -- do some work on iss53
    $ git commit -a -m 'started work on iss53'
    $ git checkout master
    $ git checkout -b hotfix
    $                       -- do some work on hotfix
    $ git commit -a -m 'fixed something hot'
    $ git checkout master
    $ git merge hotfix
    $ git branch -d hotfix  -- удалить временную ветку
    $                       -- deploy hotfix
    $ git checkout iss53    -- вернуться в первоначальную ветку
    $                       -- продолжить работу в ней
    $ git commit -a -m 'finished work on iss53'
    
Получение информации
--------------------

    $ git log --oneline --decorate
    
    $ git log --oneline --decorate --graph --all

В очередь на изучение
---------------------
* Stashing and Cleaning (in chapter "Git Tools")