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
    
### Конфликты слияния

Если в ветках подвергаемых слиянию изменяется один и тот же участок в одном и том же файле, то слияние не может быть произведено автоматически и возникает необходимость в ручном разрешении конфликта.

    $ git status            -- посмотреть какие файлы содержат конфликт слияния (unmerged)
    
Маркеры разрешения конфликта:

    <<<<<<< HEAD:index.html     -- marker
    текст версии 1
    =======                     -- marker
    текст версии 2
    >>>>>>> iss53:index.html    -- marker
    
Следует отредактировать текст и удалить маркеры разрешения конфликта.

    $ git add .                         -- обозначить что конфликт разрешен
    $ git commit -a -m 'merge message'  -- завершить коммит слияния
    
### Управление ветками

    $ git branch                -- показать список веток
    $ git branch -v             -- показать список веток с последними коммитами
    $ git branch --merged       -- ветки уже слитые в текущую ветку (могут быть удалены, кроме текущей)
    $ git branch --no-merged    -- ветки еще не слитые в текущую ветку
    
    $ git checkout testing
    $ git branch --no-merged master     -- ветки не слитые в ветку master
    
### Процесс работы с ветками

#### Постоянные Ветки 

master - ветка содержающая код, который полностью стабилен и готов к выпуску.  
develop - рабочая ветка в которой ведется разработка

    C1                          master
        C2 C3 C4 C5             develop
                    C6 C7       topic branch

Основная идея в том, что ветки находятся на разных уровнях стабильности. Когда они достигают более стабильного уровня, то сливаются в верхние ветки (более стабильные ветки).

#### Временные ветки (Topic branches)

Это ветки с короткой жизнью создаваемые под конкретную рабочую цель.

Удаленные Ветки (Remote Branches)
---------------------------------

    $ git ls-remote [remote]    -- view list of remote references
    
Remote-tracking branches are references to the state of remote branches. You can't move them. Git moves them when you do any network communication.

    $ git clone path-to-remote-repository   -- clone and get local branch 'master'
    $                               -- someone does some work on remote
    $                               -- you do some work on local 'master'
    $ git fetch origin              -- update 'origin/master' to its new state
    
    $ git push <remote> <branch>    -- push local branch to remote
    
    $ git fetch origin              -- fetches new branches from the server
    $ git checkout -b serverfix origin/serverfix    -- set up to track remote branch
    
### Отслеживание веток (Tracking Branches)

**Tracking branch** - локальная ветка, которая связана с удаленной веткой.

    $ git checkout -b serverfix origin/serverfix    -- set up to track remote branch
    $ git checkout --track origin/serverfix         -- set up to track remote branch (shortcut)
    $ git checkout <branch-name>    -- если локальная ветка не существует, но имя соответствует  
                                    -- имени remote branch, то будет создана tracking branch
    $ git branch -u origin/serverfix    -- serverfix set up to track remote from origin
    
    $ git branch -vv                    -- list local branches with tracking info
    
    $ git fetch --all; git branch -vv   -- fetch all before viewing info
    
    $ git push origin --delete serverfix    -- delete serverfix branch from the server
    
Rebasing
--------

Rebasing в итоге приводит к тому же, что и merge, но создает более чистую историю.

Before: 
 
             C4         experiment
    C0 C1 C2 C3         master        (current branch)

Rebasing:

    $ git checkout experiment
    $ git rebase master
    
After:  

             C4     
    C0 C1 C2 C3    C4'  experiment    (current branch)
             master

Fast-forward merge:

    $ git checkout master
    $ git merge experiment
    
After fast-forward merge:  

    C0 C1 C2 C3 C4'     master        (current branch)
                experiment
                
**Do not rebase commits that exist outside your repository.**

Изменение сообщения последнего коммита
--------------------------------------
    $ git commit --amend
    
Добавление файлов в последний коммит
------------------------------------
    $ ...                   -- do some work
    $ git add               -- добавить дополнительные файлы для коммита
    $ git rm                -- удалить отслеживаемые файлы
    $ git commit --amend    -- использовать текущий индекс для обновления коммита
    
'git commit --amend' изменяет SHA-1 коммита, то есть не следует править последний коммит, если он уже куда-то отправлен.

Объединение коммитов
--------------------
    $ git rebase -i HEAD~3  -- начать интерактивное редактирование

Например для объединения 3-х коммитов нужно для двух указать 'squash'

    pick f7f3f6d changed my name a bit
    squash 310154e updated README formatting and added blame
    squash a5f4a0d added cat-file
    
Затем сразу будет предложено отредактировать сообщения этих 3-х коммитов объединив их в одно.
    
Получение информации
--------------------

    $ git log --oneline --decorate
    
    $ git log --oneline --decorate --graph --all

В очередь на изучение
---------------------
* Stashing and Cleaning (in chapter "Git Tools")
* Rewriting History (in chapter "Git Tools")
