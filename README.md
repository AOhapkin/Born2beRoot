# Born2beRoot

Цель проекта: настроить виртуальую машину следуя определенным правилам из [задания](en.subject.pdf).

Использование графического интерфейса запрещено.

## Первый запуск и проверка

Проверяем структуру разделов командой `$lsblk`,
Логинимся как root `$su -` и устанавливаем [sudo](https://ru.wikipedia.org/wiki/Sudo) `$apt-get install sudo`.

## Настройка SSH

[SSH](https://ru.wikipedia.org/wiki/SSH) должен работать на порту 4242. В целях безопасности должно быть невозможно подключение по SSH через root.

Править SSH конфиг тут - `$nano /etc/ssh/sshd_config`:
* указываем порт - `Port 4242`;
* запрещаем подкл через root - `PermitRootLogin no`. 

## Выдача root-прав пользователю

`$usermod -aG root <user>`

Сохраняем бэкап конфиги sudo `$cp /etc/sudoers /etc/sudoers.backup`. 
Исправляем конфиг sudo `$nano /etc/sudoers`: 
* добавляем строку `<user>    ALL=(ALL:ALL) ALL`;
* перезагружаем `reboot`.

## Доступ через терминал

Добавляем порт `4242` в virtualbox
`$ssh <user>@localhost -p 4242`

## Настройка входа под sudo

Логинимся как root `$su -`, проверяем группы пользователей `$groups root` и `$groups <user>`.
Имя хоста - это логин + "42" (например, gmyriah42). Изменить имя машины можно с помощью:
`nano /etc/hostname`.

Правим конфиг sudo `$nano /etc/sudoers`: 
* меняем фразу при неудачном вводе `Defaults  badpass_message="Your phrase"`;
* устанавливаем кол-во попыток ввода `Defaults        passwd_tries=3`

## Создание группы юзеров
Создаем группу user42 `$addgroup user42` и помещаем туда пользователя: `$usermod -aG user42 <user>`.

## Firewall

Настройка ОС с помощью брандмауэра [UFW](https://ru.wikipedia.org/wiki/Uncomplicated_Firewall) (оставить открытым только порт 4242):
* Установка - `$apt-get install ufw` (проверить - `$ufw status`).
* Включение - `$ufw enable` (проверить - `$ufw status`).
* Дефолтные настройки - `$sudo ufw default deny incoming` и `$sudo ufw default allow outgoing`.
* Разрешаем наш порт SSH - `$ufw allow 4242`.

## Политика паролей

Мы должны настроить надежную политику паролей согласно правилам из задания (страница 6).
Делаем исправления в файле `$nano /etc/login.defs` (меняем ряд значений):
* `PASS_MAX_DAYS 30` (Максимальное число дней для использования пароля)
* `PASS_MIN_DAYS 2` (Минимальное число дней для использования пароля)
* `PASS_WARN_AGE 7` (Число дней за которое начнёт выдаваться предупреждение об устаревании пароля)

Устанавливаем утилиту для проверки паролей и генерации паролей `$apt-get install libpam-pwquality`.
Делаем бэкап `$cp /etc/pam.d/common-password /etc/pam.d/common-password.backup`.

Правим ~~вечно~~ конфиг `$nano /etc/pam.d/common-password`:
* Находим строку `password        requisite	pam_pwquality.so retry=3 maxrepeat=3 minlen=10 dcredit=-1 ucredit=-1 reject_username difok=7`
* Меняем её вот так `password        requisite	pam_pwquality.so retry=3 maxrepeat=3 minlen=10 dcredit=-1 ucredit=-1 reject_username enforce_for_root`

Меняем текущие пароли в соответствии с новой политикой:
* `$passwd user`
* `$passwd root`

## Настройка TTY:
* открываем конфигу TTY `$nano /etc/systemd/logind.conf`
* нужно раскоментиить и изменить значнения `NAutoVTs=8` и `ReserveVT=8`

## Логирование sudo
* создаем каталог логирования `$mkdir /var/log/sudo`
* создаем там файл для логов `$touch /var/log/sudo/sudo.log`
* идем с конфигу sudo `$nano /etc/sudoers`
* добавляем строку `Defaults        logfile=/var/log/sudo/sudo.log`
* делаем какое-нибудь действите под пользователем через sudo и проверяем работу логирования `$nano /var/log/sudo/sudo.log`

## Скрипт мониторинга
* для его работы устанавливаем утилиту [Net-tools](https://www.opennet.ru/docs/RUS/lfs5/appendixa/net-tools.html) `$apt-get install net-tools`
* `su user`
* `cd ~/`
* создаем файл для скрипта мониторинга`$touch monitoring.sh`
* делаем его исполняемым `$chmod +x ./monitoring.sh`
* наполняем командами`$nano monitoring.sh`

## Настройка CRON
Добавляем наш скрипт мониторинга в расписание `$crontab -e` `*/10 * * * *	sh /home/<user>/monitoring.sh`