## Использование Git с ключами SSH

https://linuxkamarada.com/en/2019/07/14/using-git-with-ssh-keys/

Сегодняшний пост предназначен для разработчиков. Если вы используете систему управления версиями Git с такими службами, как GitHub, GitLab или Bitbucket, для размещения исходных кодов проектов и управления ими, вы знаете, что по умолчанию Git подключается к удаленным удаленным устройствам по протоколу HTTPS, который требует от вас вводить имя пользователя и пароль каждый раз, когда вы запускаете такую команду, как git pullor git push.

Используя протокол SSH, вы можете подключаться и проходить аутентификацию на серверах, чтобы пользоваться их услугами. Три упомянутые службы позволяют Git подключаться по SSH вместо HTTPS. Подключение с помощью шифрования с открытым ключом позволяет не вводить имя пользователя и пароль для каждой команды Git.

В этом посте вы увидите, как использовать GitHub, GitLab и Bitbucket с SSH.

Убедитесь, что установлен SSH-клиент

Для подключения по протоколу SSH в вашей системе должен быть установлен SSH-клиент. Если вы используете openSUSE, он должен быть уже установлен по умолчанию.

Просто чтобы убедиться, откройте терминал и запустите:

$ ssh -V

Эта команда должна вывести номер версии используемого SSH-клиента:

OpenSSH_7.9p1, OpenSSL 1.1.0i-fips 14 Aug 2018

В случае, если система сообщит, что команда ssh не найдена, вы можете установить запущенный клиент OpenSSH:
zypper in openssh

Проверьте наличие существующих SSH-ключей

Для подключения по протоколу SSH вам потребуется пара ключей SSH (один закрытый, а другой открытый). Если вы никогда не использовали SSH, вы можете спокойно пропустить эту тему и перейти к следующей. Если вы когда-либо использовали SSH (например, для удаленного доступа к серверу), вероятно, у вас уже есть пара ключей SSH, и в этом случае вам не нужно создавать новую пару ключей.

Чтобы проверить наличие существующих ключей SSH, выполните:

$ ls -lah ~/.ssh

В этой команде должно быть указано содержимое ~/.sshпапки, в которой SSH-клиент хранит свои файлы конфигурации:

total 28K drwx------ 2 vinicius users 94 Mar 17 14:55 . drwxr-xr-x 54 vinicius users 4.0K Jul 14 02:44 .. -rw------- 1 vinicius users 2.5K Mar 1 23:41 authorized_keys -rw-r--r-- 1 vinicius users 39 Dec 19 2018 config -rw------- 1 vinicius users 3.3K Jul 18 2018 id_rsa -rw-r--r-- 1 vinicius users 748 Jul 18 2018 id_rsa.pub -rw-r--r-- 1 vinicius users 4.7K Jul 5 01:57 known_hosts

Если вы получите сообщение об ошибке, что в ~/.sshнем нет каталога или файлов, не волнуйтесь: это означает, что вы еще не создали пару ключей SSH. Если это так, перейдите к следующей теме.

По умолчанию открытые ключи SSH называются:

id_dsa.паб;
id_ecdsa.паб;
id_ed25519.паб; или
id_rsa.паб.

Внутри моей ~/.sshпапки у меня есть пара ключей SSH (id_rsa.pub открытый ключ и id_rsaзакрытый ключ), созданная год назад (Jul 18 2018).

По соображениям безопасности рекомендуется создавать новую пару ключей SSH не реже одного раза в год. Если у вас уже есть пара ключей SSH, созданная более года назад, рекомендуется перейти к следующему разделу.

Если у вас уже есть пара ключей SSH и вы хотите использовать ее повторно, вы можете пропустить следующий раздел. Создайте новую пару ключей SSH

Чтобы создать новую пару ключей SSH, выполните следующую команду (your_email@example.comзамените свой адрес электронной почты).:

$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

Generating public/private rsa key pair. Enter file in which to save the key (/home/your_user_name/.ssh/id_rsa):

Он спрашивает вас, где сохранить закрытый ключ (id_rsa).

Нажмите Enter, чтобы принять расположение по умолчанию.

Если у вас уже есть закрытый ключ, он спрашивает, следует ли его перезаписать:

/home/your_user_name/.ssh/id_rsa already exists. Overwrite (y/n)?

Если это произойдет, введите yи нажмите клавишу Ввод.

Затем введите и повторно введите парольную фразу (думайте об этом как о своего рода пароле).:

Enter passphrase (empty for no passphrase): Enter same passphrase again:

Пара ключей SSH создается в ~/.ssh.

Все взаимодействие должно выглядеть примерно так:

your_user_name@your_host_name:> ssh-keygen -t rsa -b 4096 -C "your_email@example.com" Generating public/private rsa key pair. Enter file in which to save the key (/home/your_user_name/.ssh/id_rsa): /home/your_user_name/.ssh/id_rsa already exists. Overwrite (y/n)? y Enter passphrase (empty for no passphrase): Enter same passphrase again: Your identification has been saved in /home/your_user_name/.ssh/id_rsa. Your public key has been saved in /home/your_user_name/.ssh/id_rsa.pub. The key fingerprint is: SHA256:CEnY8FOQmvISJpVp6oAlITemk1aWKRdViOFePP6/CKk your_email@example.com The key's randomart image is: +---[RSA 4096]----+ |o.=@X++. | |o*@O++ | |=Bo+=+ | |Oo+ oo.. | |=+ . .. S | |... o | | . o . | | . . o | | E . o. | +----[SHA256]-----+ your_user_name@your_host_name:>

Добавьте закрытый SSH-ключ в ssh-агент

Если вы не хотите вводить свою парольную фразу каждый раз при использовании ключей SSH, вам необходимо добавить ее в ssh-агент, который представляет собой программу, которая работает в фоновом режиме, пока вы входите в систему и сохраняете свои ключи в памяти.

Чтобы запустить ssh-агент в фоновом режиме, выполните следующие действия:

$ eval "$(ssh-agent -s)"

Эта команда выводит идентификатор процесса ssh-агента :

Agent pid 2887

Затем добавьте свой закрытый ключ SSH в ssh-агент:

$ ssh-add ~/.ssh/id_rsa

Введите свою кодовую фразу и нажмите Enter:

Enter passphrase for /home/your_user_name/.ssh/id_rsa:

Команда подтверждает, что закрытый SSH-ключ был добавлен в ssh-агент:

Identity added: /home/your_user_name/.ssh/id_rsa (your_email@example.com)

Добавьте открытый SSH-ключ в свою учетную запись

Как только у вас есть SSH-ключ и вы добавили его в ssh-агент, вы можете настроить подключение по SSH. Давайте посмотрим, как это сделать для каждого из трех серверов: GitHub, GitLab и Bitbucket.

Во всех трех случаях процесс аналогичен. Начните с копирования содержимого файла открытого ключа SSH (~/.ssh/id_rsa.pub) в буфер обмена с помощью команды xclip:

$ xclip -sel clip < ~/.ssh/id_rsa.pub

xclip-это утилита командной строки, которая обеспечивает доступ к буферу обмена графического интерфейса с терминала. Если он не установлен, вы можете установить его под управлением:
zypper install xclip

GitHub

Используя браузер, перейдите на домашнюю страницу GitHub по адресу github.com и войдите в свою учетную запись.

В правом верхнем углу страницы щелкните фотографию своего профиля, затем нажмите "Настройки".:

На боковой панели "Настройки пользователя" выберите ключи SSH и GPG. Затем нажмите Новый SSH-ключ.

Заполните поле заголовка описательной меткой для нового ключа (например, имя вашего компьютера) и вставьте свой открытый ключ в поле Ключа. Наконец, нажмите Добавить SSH-ключ:

Теперь ключ появится в списке SSH-ключей, связанных с вашей учетной записью: GitLab
https://htmlacademy.ru/blog/boost/frontend/git-console
Что такое SSH-ключ и зачем он нужен?

Чтобы работать со своего компьютера с GitHub, иметь доступ к проектам, хранящимся на сервисе, выполнять команды в ко нсоли без постоянного подтверждения пароля, нужно пройти авторизацию у сервера. В этом помогают SSH-ключи.

Каждый SSH-ключ содержит пару: открытый (публичный) и закрытый (приватный) ключ. Открытый ключ отправляется на сервер, его можно не прятать от всех и не переживать, что кто-то его увидит и украдёт. Он бесполезен без своей пары — закрытого ключа. А вот закрытый ключ — секретная часть. Доступ к нему должен быть только у вас.

Вы отправляете какую-то информацию на сервер, где хранится ваш публичный ключ, сервер понимает, что вы это вы, то есть идентифицирует именно вас, и даёт вам какой-то ответ. И только вы можете расшифровать этот ответ, потому что только у вас есть подходящий закрытый ключ. Получается что-то вроде связки логин-пароль только намного безопасней. Ваш пароль кто-то может узнать или подобрать, а чтобы получить ваш приватный SSH-ключ, злоумышленнику придётся взломать ваш компьютер.

Чтобы пройти авторизацию по SSH-ключу, его надо сгенерировать или найти уже ранее созданный ключ на своём компьютере.

Сначала проверим, есть ли уже на компьютере ключ. По умолчанию SSH-ключи хранятся в каталоге ~/.ssh, поэтому нужно проверить содержимое этого каталога.

Открываем консоль.
Вводим cd ~/.ssh, чтобы перейти в нужный каталог.
Переходим в нужную директорию
Переходим в нужную директорию.
Используем ls, чтобы увидеть список всех файлов в каталоге.
Открываем список файлов в директории
Открываем список файлов в директории.
Ищем пару файлов с названиями вида имя и имя.pub. Обычно имя — id_rsa, id_dsa, id_ecdsa или id_ed25519. Файл с расширением .pub — ваш публичный ключ, а второй — ваш приватный, секретный ключ. Если таких файлов или даже каталога .ssh у вас нет, вы можете их сгенерировать. Для этого делаем следующее.
    Открываем консоль и вводим команду:

    ssh-keygen -t rsa -b 4096 -C "your_mail@example.com"

    Указываем тот адрес электронной почты, который вводили при регистрации на GitHub.
    Генерируем ключ
    Генерируем ключ.
    Далее нужно указать расположение файла для сохранения ключа. Если вы не введёте путь до файла и просто нажмёте Enter, ключ сохранится в файле, указанном в скобках.
    Теперь нужно установить пароль к вашему ключу и дважды ввести его. Если вы не хотите вводить пароль каждый раз, когда используете ключ, пропустите этот шаг, нажав «Enter», и ничего не вводите.
    Указываем расположение ключа и вводим пароль
    Указываем расположение ключа и вводим пароль.
Добавляем ключ в ssh-agent (сгенерированный или уже существующий). Проверяем доступность ключа командой eval "$(ssh-agent -s)" и добавляем с помощью ssh-add ~/.ssh/your_key_name, где указываем верный путь до файла с ключом и его имя.
Добавляем ключ в shh-agent
Добавляем ключ в shh-agent.
Несколько важных примечаний:
    Если вы захотите переименовать ключ, могут возникнуть проблемы. Их можно решить, добавив в ~/.ssh/config связь ключа с доменом.
    Если у вас Windows и вы пользуетесь программой Cmder, возможны проблемы с командой eval "$(ssh-agent -s)". Может появиться такое сообщение об ошибке: «eval не является внутренней или внешней командой, исполняемой программой или пакетным файлом».

    В Сmder для запуска ssh-agent можно использовать команду start-ssh-agent.

    Если проблема осталась, рекомендуем работать в Git Bash.
    Если у вас macOS Sierra версии 10.12.2 и выше, нужно изменить ваш ~/.ssh/config файл, чтобы автоматически загрузить ключи в ssh-agent и хранить пароли.

    Host *
     AddKeysToAgent yes
     UseKeychain yes
     IdentityFile ~/.ssh/id_rsa

    Вы можете добавить свой приватный ключ в ssh-agent и сохранить пароль к нему с помощью команды ssh-add -K ~/.ssh/id_rsa. Если у вашего ключа другое имя, не забудьте заменить id_rsa в команде на правильное название.
    Если у вас Linux, может понадобится переназначить для ~/.ssh права доступа командой chmod 700 ~/.ssh/
После того как создан ключ, его нужно добавить на GitHub. Для этого копируем его содержимое с помощью одной из следующих команд:
    Если вы на Windows clip .
    Для пользователей macOS pbcopy .
    На Linux используйте sudo apt-get install xclip, чтобы установить необходимый для копирования пакет xclip, а затем введите xclip -sel clip . Или введите команду cat ~/.ssh/id_rsa.pub, контент документа появится прямо в консоли и вы сможете скопировать ключ оттуда.

    Можно пойти другим путём, открыть файл id_rsa.pub прямо в папке и просто скопировать содержимое оттуда.
Переходим на страницу для работы с ключами в вашем профиле на GitHub.
Страница с настройками ключей в вашем профиле
Страница с настройками ключей в вашем профиле.

Нажимаем кнопку New SSH key (новый SSH-ключ). Вводим имя ключа (можно придумать абсолютно любое) в поле Title (название), а в Key (ключ) вставляем сам ключ из буфера обмена. Теперь нажимаем Add SSH key (добавить SSH-ключ).
Добавляем в свой профиль SSH-ключ
Добавляем в свой профиль SSH-ключ.

Если всё сделано верно, в списке появится новый ключ.
Успешно добавленный ключ
Успешно добавленный ключ.

Теперь, наконец-то, мы можем начать работу с самим проектом.
