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

## Firewall

Настройка ОС с помощью брандмауэра [UFW](https://ru.wikipedia.org/wiki/Uncomplicated_Firewall) (оставить открытым только порт 4242):
* Установка - `$apt-get install ufw` (проверить - `$ufw status`).
* Включение - `$ufw enable` (проверить - `$ufw status`).
* Дефолтные настройки - `$sudo ufw default deny incoming` и `$sudo ufw default allow outgoing`.
* Разрешаем наш порт SSH - `$ufw allow 4242`.

