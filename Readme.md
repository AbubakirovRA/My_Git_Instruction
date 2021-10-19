# Инструкция по работе с Git и с GitHub
## Что такое система контроля сохранения версий?
Система контроля версий - это специальная программа, которая позволяет администрировать процесс создания какого-либо файла (файлов) одновременно большим количеством людей, фиксируя все изменения с указанием авторства, хронологии создания, а также с возможностью одновременного ведения нескольких веток, в которых происходят изменения различных версий одного и того же файла, с последующим утверждением окончательной версии и слиянием утвержденных изменений.

Очень неплохой мануал лежит [здесь,](https://habr.com/ru/post/522078/), а также [здесь](https://githowto.com/ru) и [здесь](https://bookflow.ru/shpargalka-po-git/)
## Что такое Git?
Git - это одна из реализаций систем контроля версий. Git позволяет управлять изменениями, создавать фиксации, ветки, а также сливать их. 
## Подготовка репозитория
Репозиторий - это хранилище файлов, поддерживающих версионность. Создать репозиторий можно с помощью применения в папке команды *git init*

команда _**git init**_ запущенная git-ом в какой-либо папке превращает эту папку в репозиторий, то есть в хранилище. Это значит, что в этой папке git начинает отслеживать изменения всех файлов любого типа. После этой команды он создает в папке собственную служебную структуру папок и файлов.

после того, как Вы создали файл в отслеживаемой папке, нужно указать git, что этот файл должен быть отслеживаем, это делается командой 

_**git add <имя файла>**_ 
## Создание сохранений
Далее, после того, как Вы указали, что файл нужно отслеживать, мы можем создавать историю изменений этого файла, создавая "точки восстановления". 

Мы можем создавать "сохранения" наших версий файлов. Такие "сохранения" называются фиксациями или комитами.

Сделать комит можно с помощью команды *git commit* и **ОБЯЗАТЕЛЬНО** использовать флаг -m после чего в кавычках написать сообщение: 

_**git commit -m "Текст Вашего комментария"**_
## Перемещение между сохранениями
Перемещаться между нашими сохранениями можно с помощью команды *Git checkout*. Для этого достаточно применить команду *git checkout <номер сохранения>*.

Кроме того, данная команда позволяет переключаться с одной ветки на другую, при наличии нескольких веток в  дереве проекта. Например, вот так: *git checkout <new branch name>*
Можно отменить изменения с помощью команд *git revert*, *git reset*
*git revert <номер комита>* отменит изменения до указанной версии и создаст новый комит. 
*git reset --hard <номер комита>* - отменит изменения до указанного комита и затрет всю историю изменений после этого комита.
## Журнал изменений
Журнал изменений вызывается при помощи команды _**git log**_ - при вызове данной команды на экран выводится вся история созданных комитов с указанием их хэша и содержанием коментариев, которые были созданны при записи комитов.

Более "продвинутая" версия этой команды вызывается при помощи ключа --graph:

 _**git log --graph**_

В этом случае git показывает не только историю создания всех коммитов, но также при помощи средств псевдографики изображает иерархическую схему созданных комитов.
## Ветки в Git
Новая ветка создается с использованием команды git branch
## Слияние веток и решение конфликтов

## Удаление веток

Удалить ветку можно при помощи команды git branch -d <имя ветки>

## Скачивание удаленного репозитория
Скачать удаленный репозиторий возможно при помощи команды "git clone". Выполняется это так 

# Git setup Instruction #

### Download the Git:

follow this links:
+ [Windows](https://git-scm.com/download/win)
+ [MacOS](https://git-scm.com/download/mac)
+ [Linux](https://git-scm.com/download/linux)
### Install it on your computer
### Next, you need to configure Git: 

To show all insalled setup use command with a keys:

git config --list --show-origin

First, that you need to do to setup Git:
----------------------------------------
you need to enter your name and your email address. For example:

+ **git config --global user.name "AbubakirovRA"**

+ **git config --global user.email AbubakirovRA@yandex.ru**

Several commands for working in Git
===================================
 + **git version** - show version installed Git
 
 + **git init** - initialisation current workdirectory to repository
 + **git status** - show all change in tracking directory
+ **git add** - add file to tracking of change in current directory
+ **git commit** - creating the "point of recovery" current directory - repositry. If use a key -m, than posible add message for current "point of recovery"
+ **git log** - show all commits an current repository
+ **git checkout** - restore specified commit
+ **git checkout master** - return to master branch
+ **git diff** - compare current save with previous version


detailed install and setup instructions can be found at the link: [Install&Setup](https://git-scm.com/book/ru/v2/)

  ![Picture](https://gbcdn.mrgcdn.ru/uploads/avatar/3320581/attachment/thumb-da6ca1bdffc83bcc292b06d091d82d14.png "GeekBrains-AbubakirovRA")
