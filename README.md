# Born2beRoot (сценарий защиты проекта)

Цель проекта: настроить виртуальую машину следуя определенным правилам из [задания](en.subject.pdf).
Использование графического интерфейса запрещено.

## Обзор проекта

### Как работает ВМ?

Виртуальная машина - это  программная, которая работает как компьютер. Она имитирует процессор и другие физическое железо. ВМ понимает машинный язык, который мы можем использовать для программирования.
Работая на одной машине мы может тестировать наше ПО на разных архитектурах.

### Какую ОС выбрал и почему?

Выбрал DebiaN, т.к. его советуют для новичков в администрировании.

### Разница между CentOS и Debian

Debian - это волонтёры, а CentOS - компания RedHat
У Debian больше всего пакетов в офиц репозиториях и множество стронних с различным доп и новыми версиями ПО. Менеджер пакетов APT. Обновляется нерегулярно.

У CentOS пакетов меньше (для настройки сервера может быть достаточно), но их так же много в сторонних репозиториях. Менеджер пакетов YUM.

Обе системы очень стабильные.

У Debian очень большое русскоговорящее сообщество.

### Для чего нужна ВМ

- Тестирование ПО
- Разработка в безопасной среде
- Знакомство с разными ОС
- Размещение ПО на удаленных серверах

### Разница Aptitude и Apt. Что такое AppArmor?

Aptitude и Apt-get - это пакетные менеджеры. Позволяют устанавливать, удалять, обновлять или искать пакеты.
Apt - утилита командной строки.
Aptitude - это оболочка для APT, которая добавляет больше функционала.

Aptitude автоматически удаляет неиспользуемые пакеты и все их зависимости. Apt-get удаляет только то что было указанно в команде.

AppArmor - программа определяющая к каким сист ресурсам и с какими привилегиями может получить доступ то или иное приложение.
AppArmor - это не броня приложений, а смирительная рубашка для них.

## Начало проверки машины

- Залогиниться под юзером (не под root)
- Проверить, что UFW вкл `sudo ufw status verbose`
- Проверить, что сервис SSH вкл `sudo service ssh status`
- Показать название ОС `uname -a`

## User

- Проверить, что юзер входит в группы sudo и user42 `groups`
- Политика паролей: `cat /etc/pam.d/common-password` и `cat /etc/login.defs`
- Создать нового юзера `sudo adduser <username>`
- Проверить применение политики пароля к нему `sudo chage -l <username>`
- Создаем группу evaluating `addgroup evaluating`
- Добавляем нового юзера в группу `addgroup evaluating <username>`
- Проверка `groups`


## Hostname

- Проверить, что хостнейм - "логин+42" `hostname`
- Изменить на "логин_проверяющего+42" и ребутнуться, проверив, что хостнейм сменился `sudo hostnamectl set-hostname <username>42`
    //  `sudo reboot`
- Вернуть старый хостнейм `sudo hostnamectl set-hostname trbeardl42`
    //  `sudo reboot`
    //  `hostname`
- Проверить разбивку на разделы и сравнить `lsblk`
- Рассказать про LVM

## SUDO

- Проверить, что судо установлена на машине `dpkg -l | grep sudo`
- Добавить какого-нибудь пользователя в группу судо
    //  sudo adduser <username> sudo
    //  getent group sudo
    //  sudo visudo ?
- Объяснить, как работает sudo
        // sudo (Substitute User and do, дословно «подменить пользователя и выполнить»)
        // — программа для системного администрирования UNIX-систем, позволяющая делегировать
        // те или иные привилегированные ресурсы пользователям с ведением протокола работы.
        // Основная идея — дать
пользователям как можно меньше прав, при этом достаточных для
        // решения поставленных задач
- Сгонять в папку `/var/log/sudo`, проверить что она вообще есть, и что в ней есть файлы,
    проверить их содержимое. Проверить, что при выполнении какой-либо команды от судо логгируется в журнал.
    // `sudo ls /var/log/sudo/`
    // `sudo cat /var/log/sudo/sudolog`
    // `sudo ls`
    // `sudo cat /var/log/sudo/sudolog`


## UFW

- Проверить, что UFW корректно установлена на машине
    `dpkg -l | grep ufw`
- Проверить, что UFW работает
    `sudo service ufw status`
- Рассказать про UFW и зачем его юзать
    // Uncomplicated Firewall это утилита для конфигурирования межсетевого экрана     Netfilter.
    // Она использует интерфейс командной строки, состоящий из небольшого числа простых команд.
- Вывести все активные правила в UFW и убедиться, что там есть правило для порта 4242
    //  `sudo ufw status numbered`
- Добавить новое правило для порта 8080 и проверить, что оно добавилось
    //  `sudo ufw allow 8080`
    //  `sudo ufw status numbered`
- Удалить это правило
    //  `sudo ufw delete <number>`

## SSH

- Проверить, что SSH правильно установлен на машине `sudo service ssh status`
- Проверить, что он норм работает (подключиться через него)
- Объяснить, что такое SSH и зачем он нужен.
    // SSH, также известный как Secure Shell или Secure Socket Shell, представляет собой
    // сетевой протокол, который предоставляет пользователям, особенно системным
    // администраторам, безопасный способ доступа к компьютеру по незащищенной сети.
- Убедиться, что SSH слушает только 4242 порт `sudo cat /etc/ssh/sshd_config`
- Проверить, что нельзя зайти на сервак под рутом `ssh root@localhost -p 4242`
- Зайти на сервак через ssh как новый пользователь (которого создали при проверке) `ssh <username>@localhost -p 4242`

## Monitoring

- Показать код скрипта и объяснить его
    // `sudo nano /usr/local/bin/monitoring.sh`
- Объяснить, что такое cron
    // Планировщик заданий Cron - это один из компонентов операционной системы Linux.
    // Он используется для запуска на хостинге  определенных  скриптов  в нужное время, по расписанию.
    // Примеры таких заданий:    отправка e-mail с отчетом о работе сайта за сутки;
                                создание резервной копии сайта;
                                резервное копирование БД;
- Рассказать, как запускать скрипт каждые 10 минут
    //  `sudo crontab -u root -e`
- Сделать так, чтоб скрипт запускался каждую минуту
- Сделать так, чтоб скрипт не запускался каждую минут после перезагрузки сервака.
    `sudo /etc/init.d/cron stop` //
    `sudo /etc/init.d/cron start`
