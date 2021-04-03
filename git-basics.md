[Index](README.md)  
[git-distributed](git-distributed.md)

Git.
====

Файлы могут быть в трех состояниях
----------------------------------
* committed - данные сохранены в локальной базе
* modified - файл изменен, но не сохранен в локальной базе
* staged - измененный файл в его текущем состоянии помечен для сохранения в следующем коммите

Setup Identity
--------------
    $ git config --global user.name "Your Name"
    $ git config --global user.email yourmail@example.com
    $ git config --list
    $ git config specific.key.value.to.view

    $ git config --global core.autocrlf true    --> if on Windows
    $ git config --global core.autocrlf input    --> if on Linux
    
    $ git config --global core.editor "code --wait"     --> set VSCode as default editor

    $ git config --global -e     --> open default editor for global settings

### Set visual tool for Diff
    $ git config --global diff.tool vscode
    $ git config --global difftool.vscode.cmd 'code --wait --diff $LOCAL $REMOTE'
    $ git difftool      --> invoke the chosen visual tool
    
### Проверка источника (файла конфигурации) для конкретного параметра настройки

    $ git config --show-origin key.value.to.check
    
### Как сделать чтобы директория была отслеживаема Гитом

    mkdir dirname
    touch dirname/.gitkeep  --> директория содержащая файл теперь может быть добавлена в репозиторий

Основные команды
================

### Просмотр статуса
    $ git status
    $ git status -s         --> статус в краткой однострочной форме
    
### Просмотр изменений
    $ git diff              --> изменения, еще не помещенные в stage (между working dir и stage area)
    $ git diff --staged     --> изменения помещенные в stage (между stage area и repository)
    $ git diff --cached     --> то же что и --staged
    $ git diff HEAD~2 HEAD  --> изменения между двумя коммитами
    $ git diff HEAD~2 HEAD filename  --> изменения между двумя коммитами для конкретного файла
    $ git diff HEAD~2 HEAD --name-only   --> изменения между двумя коммитами (вывести только имена файлов)
    
### Файловые операции
    $ git rm <file>             --> удаляет файл из stage и working dir (нужно закоммитить это)
    $ git rm --cached <file>    --> удаляет файл только из staged, оставляя в working dir (нужно коммитить)
    $ git rm log/\*.log         --> удаляет файлы с расширением .log из директории log (backslash \ важен)
    $ git rm \*~                --> удаляет все файлы оканчивающиеся на ~
    $ git mv <from> <to>        --> переименовать (переместить)

### Staging Area
    $ git ls-files              --> List files in staging area (index)
    $ git rm --cached <file>    --> Remove file from staging area (need to commit), so it is not tracked any more

### Commit

    $ git commit -a -m message - коммитит измененные файлы если они уже отслеживаемые

### Tagging
    $ git tag v1.0 5e7a828      --> прицепить тэг к указанному коммиту
    $ git checkout v1.0         --> checout коммит по указанному тэгу
    $ git tag                   --> показать все созданные тэги
    $ git tag -a v1.1 -m "My version 1.1"   --> создать аннотированный тэг с описанием и прицепить к тек.ком.
    $ git tag -n                --> показать тэги с сообщениями коммитов и их собственными описаниями

    $ git push origin v1.0       --> push tag to remote
    $ git push origin --delete v1.0     --> deelte the specified tag from a remote
    $ git tag -d v1.0            --> delete tag locally


### История коммитов
    
#### Просмотр истории коммитов
    $ git log
    $ git log file.txt      --> показать коммиты изменившие файл file1.txt
    $ git log -2            --> показать последние 2 коммита
    $ git log --oneline     --> вывести информацию по каждому коммиту в одной строке
    $ git log --oneline --stat      --> показать все файлы изменные в коммитах
    $ git log --oneline --patch      --> показать все изменения в файлах
    $ git log --all         --> вывести все коммиты, в т.ч. после текущей ветки
    $ git log --patch       --> показать изменения внесенные каждыйм коммитом
    $ git log -p            --> тоже что и --patch
    $ git log --state       --> показать статистику (кол-во измененных файлов, удаленных/добавленных строк)
    $ git log --pretty=oneline                          --> вывод в одну строку (sha-1, комментарий)
    $ git log --pretty=format:"%h - %an, %ar : %s"      --> hash, author name, author relative date, subject
    $ git log --pretty=format:"%h %s" --graph           --> hash, subject, ascii-graph
    $ git log --oneline --all --decorate --graph        --> показать граф с отображением всех веток
    
#### Ограничение вывода истории коммитов
    $ git log -<n>              --> вывод последних n коммитов
    $ git log --since=2.weeks   --> вывод за последние 2 недели
    $ git log --before="2020-09-01"
    $ git log --after="2020-09-01"
    $ git log --after="yesterday"
    $ git log --after="one week ago"
    $ git log --author="Author name"          --> только коммиты определенного автора
    $ git log --grep="search pattern"         --> поиск по ключевым словам в описании
    $ git log -S function_name  --> показать коммиты изменившие кол-во вхождений указанной подстроки (добавление/удаление)
    $ git log -S"changed-substring" --patch     --> коммиты изменившие указанную подстроку
    $ git log --oneline hash_from..hash_to      --> диапазон коммитов from/to указанные хэши граничных коммитов
    $ git log --oneline toc.txt                 --> коммиты затронувшие файл toc.txt
    $ git log --oneline -- toc.txt                 --> тоже что предыдущий, но если git жалуется на имя файла
    $ git log --oneline --patch -- toc.txt         --> тоже что предыдущий, но с внесенными изменениями

#### Форматирование вывода истории
    $ git log --pretty=format:"%an %H"     --> author name, full hash
    $ git log --pretty=format:"%an %h"     --> author name, short hash
    $ git log --pretty=format:"%an commited %h on %cd"     --> author name, short hash, commit date

#### Просмотр коммита
    $ git show 921a2ff      --> shows the given commit
    $ git show HEAD         --> shows the last commit
    $ git show HEAD~2       --> two steps before the last commit
    $ git show HEAD:file.js --> shows the exact version of file.js stored in the last commit
    $ git show HEAD --name-only     --> shows only names of the chanded files
    $ git ls-tree HEAD~1    --> shows all files and directories stored in the commit

#### История файла
    $ git log file1.txt               --> коммиты затронувшие файл file1.txt
    $ git log --oneline file1.txt     --> коммиты затронувшие файл file1.txt
    $ git log --oneline --stat file1.txt     --> коммиты затронувшие файл file1.txt с кол-вом изменений
    $ git log --oneline --path file1.txt     --> коммиты затронувшие файл file1.txt с текстом изменений


Checkout Коммита
================

    $ git checkout ab123ef4


Find Bugs using Bisect
======================
    
    $ git bisect start
    $ git bisect bad        --> tell that the current commit is 'bad'
    $ git bisect good commit-hash        --> tell that the specified commit is 'good'
    $ git log --oneline --all           --> view all commits with referenes 'good'/'bad'
    $ git bisect good       --> tell that the current 'middle' commit is 'good'
    ...continue bisecting and applying tests, dividing the history in half...
    $ git bisect bad        --> when only one commit left - so you found the first 'bad' commit
    $ git bisect reset      --> when you found the bug - attach HEAD back to master branch

Aliases
=======

    $ git config --global alias.lg "log --pretty=format:'%an commited %h'"
    $ git config --global alias.lg "log --oneline --all --graph"
    
Работа с 'Remote' Repositories
==============================
    
    $ git remote            --> перечислить имена remote repos
    $ git remote -v         --> перечислить repos с соответствующими им URLs
    
### Добавление Remote Repository
    $ git remote add <shortname> <url>
    
### Получение данных из Remotes
    $ git fetch <remote>    --> получает из remote repo новые данные (в т.ч branches)
    $ git fetch origin      --> получает из origin новые данные, но не делает merge
    $ git pull              --> fetch и merge текущую branch, если она настроена на отслеживание remote branch
    $ git pull --rebase     --> если есть расхождение веток, то применить local changes поверх remote changes
    $ git fetch --prune     --> fetch и если в remote были удалены ветки, то они будут удалены и локально
    
### Отправление данных на Remote
    $ git push <remote> <branch>
    
### Получение информации о Remote
    $ git remote show <remote>      --> показывает URL и tracking info, а также новые branches


Branches (создание, удаление, слияние)
======================================

### Создание новой ветки

    $ git branch testing - создает новый указатель на текущий коммит  
    (HEAD продолжает указывать на последнюю текущую ветку - чаще всего это master)

    $ git switch -C testing     --> создать ветку и переключиться в нее
    
### Переключение веток

    $ git checkout testing - перемещает HEAD на ветку testing

Переключение веток помимо перемещения HEAD также меняет файлы в рабочей директории, чтобы они соответствовали последнему коммиту ветки, на которую происходит переключение.

### Простое переключение веток и слияние

Сценарий использования веток:

1. Создание ветки для новой функциональности
2. Работа в этой ветке
3. Возникает необходимость срочного изменения в основную ветку
4. Переключение в основную ветку, при этом важно иметь working dir в чистом состоянии (commit changes OR stash)
5. Создание ветки hotfix от основной ветки
6. Merge hotfix после тестирования в основную ветку, push
7. Переключение в исходную ветку п.1


    $ git checkout -b iss53     -- создать новую ветку iss53 и переключиться на нее
    $ ...                       -- do some work on iss53
    $ git commit -a -m 'started work on iss53'
    $ git checkout master
    $ git checkout -b hotfix    -- создать ветку hotfix
    $ ...                       -- do some work on hotfix
    $ git commit -a -m 'fixed something hot'
    $ git checkout master
    $ git merge hotfix          -- слить hotfix в master
    $ git branch -d hotfix      -- удалить временную ветку hotfix
    $ ...                       -- deploy изменения
    $ git checkout iss53    -- вернуться в первоначальную ветку
    $ ...                   -- продолжить работу в ней
    $ git commit -a -m 'finished work on iss53'
    
### Простое слияние
    $ git checkout master       --> переключиться в целевую ветку
    $ git merge iss53           --> слить ветку iss53 в ветку master
    $ git branch -d iss53       --> удалить ветку iss53
    
### Простое слияние без fast-forward
    $ git merge --no-ff branch_name     --> создает дополнительный коммит для слияния

### Squash-Merging

Создается отдельный коммит содержащий все изменения из слияемой ветки и этот коммит применяется поверх master (целевой ветки). Применяется для коротких веток с "не опрятной" историей из нескольких коммитов, которые можно потом удалить.

    $ git switch master                         --> перейти в целевую ветку
    $ git merge --squash branch_to_merge        --> подготавливает merge (изменения пока только в staged area)
    $ git commit -m "Descriptive message"       --> commit подготовленные squashed изменения в master

Использованная ветка не появится в списке `git branch --merged` так как технически она не сливалась.

    $ git branch -D squashed_branch             --> удалить использованную ветку насильно (так как она не merged)Ж

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

    $ git add .                         -- обозначить что конфликт разрешен (для отредактированных файлов)
    $ git status                        -- проверить, что конфликт разрешен
    $ git commit -a -m 'merge message'  -- завершить коммит слияния
    
### Управление ветками

    $ git branch                -- показать список веток
    $ git branch -v             -- показать список веток с последними коммитами
    $ git branch -a             -- показать список всех веток
    $ git branch --merged       -- ветки уже слитые в текущую ветку (могут быть удалены, кроме текущей)
    $ git branch --no-merged    -- ветки еще не слитые в текущую ветку
    $ git branch -r             -- показать ветки на remote repository
    
    $ git brahch -d branch-to-remove    -- удалить ветку
    
    $ git checkout testing              -- перейти на ветку testing
    $ git checkout -b my-branch         -- перейти на ветку my-branch и перед этим создать ее
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
    
Remote-tracking branches are references to the state of remote branches. You can't move them. Git moves them when you do any network communication. Они напоминают, где были ветки в remote repos во время последней коммуникации.

    $ git clone path-to-remote-repository   -- clone and get local branch 'master'
    $                               -- someone does some work on remote
    $                               -- you do some work on local 'master'
    $ git fetch origin              -- update 'origin/master' to its new state
    
    $ git push <remote> <branch>    -- You push local branch to remote
    
    $ git fetch origin              -- Collaborator fetches new branches from the server
    $ git merge origin/serverfix    -- Collaborator merges serverfix в текущую рабочую ветку
    
#### Если нужно создать локальную ветку от удаленной и отслеживать ее
    $ git checkout -b serverfix origin/serverfix    -- set up to track remote branch
    
### Отслеживание веток (Tracking Branches)

**Tracking branch** - локальная ветка, которая связана с удаленной веткой. Если вы находитесь на tracking branch и делаете **pull**, то git автоматически знает, с какого сервера брать данные и в какую ветку делать слияние.

    $ git checkout -b serverfix origin/serverfix    -- set up to track remote branch
    Это тоже самое, что и с --track:
    $ git checkout --track origin/serverfix         -- set up to track remote branch (shortcut)
    Еще один shortcut:
    $ git checkout <branch-name>    -- если локальная ветка не существует, но имя соответствует  
                                    -- имени remote branch, то будет создана tracking branch
    Если уже есть локальная ветка serverfix:
    $ git branch -u origin/serverfix    -- serverfix set up to track remote from origin
    
    $ git branch -vv                    -- list local branches with tracking info (разница между local и remote)
    Чтобы увидеть обновленную актуальную информацию с сервера:
    $ git fetch --all; git branch -vv   -- fetch all before viewing info

### Как запушить локальную ветку

    $ git branch -r             --> see remote tracking branches
    $ git push -u origin name_of_branch_to_push     --> push the specified branch to remote
    $ git push -d origin name_of_branch_to_delete   --> delete the remote branch
    $ git branch -r             --> confirm that the remote branch was deleted
    $ git branch -d name_of_local_branch_to_delete  --> delete the local branch without upstream
    
### Pulling
    $ git pull      -- сделает fetch, затем merge для текущей локальной ветки, если она связана с remote branch
    
### Удаление remote branch
    $ git push origin --delete serverfix    -- delete serverfix branch from the server


## [Collaboration Workflows using Branches](git-workflows.md)


Fixing Bugs Workflow
--------------------

1 - Create branch:  

    $ git branch bugfix
    $ git branch
    $ git status

2 - Switch branch (in new git):

    git switch bugfix

3 - Rename branch (optional):

    git branch -m bugfix bugfix/signup-form

4 - Make changes in the code

5 - View status:

    git status

6 - Add and commit:

    git add changed-filename
    git commit -m "Commit description"

7 - View updated log:

    git log --oneline

8 - Switch to master branch:

    git switch master
    git log --oneline --all

9 - Compare branches:

    git log master..bugfix/signup-form      --> list commits that are not in master
    git diff master..bugfix/signup-form     --> view changes between branches
    git diff bugfix/signup-form             --> shorter command when on master
    git diff --name-only bugfix/signup-form --> show only names
    git diff --name-status bugfix/signup-form --> show only names with status

10 - Change code in master branch and Stash not committed changes (optional):

    git stash push -m "Description"         --> stash tracked files
    git stash push --all -m "Description"   --> stash all including new non-tracked files
    git stash list                          --> list stashes
    ...now state of master branch is clean and you can switch between branches...

11 - View and restore saved stashes (optional):

    git stash show stash@{1}        -- show stash 1
    git stash show 1                -- same but shorter
    git stash apply 1               -- apply stash
    git stash drop 1                -- delete stash
    git stash clear                 -- delete all stashes

12 - Merge branches

13 - View and delete merged branches

    git branch --merged         --> view list of merged branches that are safe to delete
    git branch -d bugfix/login-form     --> delete branch
    git branch --no-merged      --> view list of non merged branches (not safe to delete)

    
Rebasing
========

С помощью rebasing все изменения, произведенные в одной ветке, применяются к другой ветке.
Rebasing в итоге приводит к тому же, что и merge, но создает более чистую (линейную, без ветвлений) историю. Часто применяется для коммитов во внешние проекты. Rebasing проигрывает изменения из одной линии работы на другую в том порядке, в котором они были сделаны, в то время как merge берет конечные точки и сливает их вместе.

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

После rebase можно вернуться в master и сделать fast-forward merge:

    $ git checkout master
    $ git merge experiment
    
After fast-forward merge:  

    C0 C1 C2 C3 C4'     master        (current branch)
                experiment
                
Более сложный Rebase
--------------------

    C1 C2 C5 C6         master
          C3 C4 C10     server
             C8 C9      client
             
Чтобы внести изменения в client в основную ветку, но придержать изменения в server, пока они не протестированы:

    $ git rebase --onto master server client    
    то есть взять изменения client по сравнению с server и применить их к master
    
Результат:

             master
    C1 C2 C5 C6 C8' C9' client
          C3 C4 C10     server
          
После чего можно делать fast-forward:

    $ git checkout master
    $ git merge client
    
    C1 C2 C5 C6 C8' C9' master, client
          C3 C4 C10     server
          
#### Применение изменений в topic branch поверх base branch
    $ git rebase <basebranch> <topicbranch>     --> checks out topicbranch и replays it onto the base branch
    $ git rebase master server                  --> накладывает изменения в server поверх master
    $ git checkout master
    $ git merge server              --> fast-forward merge
                
### Опасности Rebasing

**Do not rebase commits that exist outside your repository.**


Cherry-Picking
==============

Если хотем взять коммит из другой ветки и добавить эти изменения в текущую ветку:

    $ git switch target_branch                      --> переключиться в целевую ветку
    $ git cherry-pick hash_of_the_picked_commit     --> выбрать и применить выбранный коммит указав его хэш

#### Взять из другого коммита только отдельный файл

    $ git switch target_branch                      --> переключиться в целевую ветку
    $ git restore --source=branch_to_take_file_from -- file_name_to_take
    ...then add and commit the changed file...

Отмена/изменение действий
=========================

### Изменение сообщения последнего коммита

    $ git commit --amend    --> если нет изменений файлов, то вызывается редактор с комментарием к последнему коммиту
    
### Добавление файлов в последний коммит

    $ ...                   -- do some work
    $ git add               -- добавить дополнительные файлы для коммита
    $ git rm                -- удалить отслеживаемые файлы
    $ git commit --amend    -- использовать текущий индекс для обновления коммита
    
'git commit --amend' изменяет SHA-1 коммита, то есть не следует править последний коммит, если он уже куда-то отправлен. Назначение --amend  в том, чтобы внести незначительные изменения в последний коммит, при этом старый коммит полностью заменяется новым, дополненным коммитом.

### Удаление файла из Staged Area
    
    $ git reset HEAD <file>     --> результат: файл убран из индекса, но остался в working dir
    
### Отмена незакоммиченных изменений

    $ git checkout -- <file>    --> результат: рабочая копия заменяется копией из последнего коммита
    
Для сохранения изменений, но не в текущей последовательности коммитов (ветке), следует использовать Stashing и Branching.

### Возврат все отслеживаемых файлов в состояние последнего коммита

    $ git reset --hard          --> результат: все файлы помещенные в репозиторий будут возвращены в состояние последнего коммита, изменения в рабочей директории будут потеряны

### Отмена последнего коммита (только если история еще не была разделена с другими участниками проекта)

    $ git reset --soft HEAD^        --> файлы в working dir и index остаются нетронутыми
    $ git reset --soft HEAD~1

    или 

    $ git reset --hard HEAD^        --> файлы в working dir и index возвращаются в предыдущее состояние
    $ git reset --hard HEAD~1

### Отмена последнего коммита (только если история уже  была разделена с другими участниками проекта)

    $ git revert HEAD               --> сделать revert целевого коммита (HEAD, то есть последнего)
    $ git revert -m 1 HEAD          --> в случае если это revert слияния, то нужно указать parent
    
Восстановление файла из определенного коммита
---------------------------------------------

    $ git checkout ab0e87d -- <path-to-file-to-restore>

Объединение коммитов
--------------------
    $ git rebase -i HEAD~3      --> интерактивное объединение 3-х последних коммитов

В появившемся окне редактора коммиты показываются **в порядке сначала старые, потом новые**. Перед номером коммита стоит команда указывающая, что нужно с ним сделать.  
pick - использовать коммит в истории  
squash - слить изменения данного коммита в предыдущий  
После сохранения и закрытия файла откроется окно редактора для внесения поправок в комментарии.  

Например для объединения 3-х коммитов нужно для двух указать 'squash'

    pick f7f3f6d changed my name a bit
    squash 310154e updated README formatting and added blame
    squash a5f4a0d added cat-file
    
Затем сразу будет предложено отредактировать сообщения этих 3-х коммитов объединив их в одно.

Для отправки на remote repository следует использовать --force:

    $ git push <remote> <branch> -f     --> насильственный push коммитов объединенных в один коммит
    
Получение информации
--------------------

    $ git log --oneline --decorate
    
    $ git log --oneline --decorate --graph --all


Получение информации о контрибьютерах
-------------------------------------
    git shortlog          --> показать контрибюторов и их summary messages из коммитов


Нахождение автора строк используя blame
---------------------------------------
    git blame filename      --> показать все строки файла с указанием коммита и автора
    git blame -e filename      --> показать все строки файла с указанием коммита, автора и email
    git blame -L 1,3 filename      --> показать диапазон строк файла с указанием коммита и автора

В очередь на изучение
---------------------
* Distributed Git
* Stashing and Cleaning (in chapter "Git Tools")
* Rewriting History (in chapter "Git Tools")

[git-distributed](git-distributed.md)  
[Top](#git-)  
