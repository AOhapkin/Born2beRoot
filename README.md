# Born2beRoot

Цель проекта: настроить виртуальую машину следуя определенным правилам из [задания](en.subject.pdf).

Использование графического интерфейса запрещено.

##Первый запуск и проверка

Проверяем структуру разделов командой `$lsblk`,
Логинимся как root `$su -` и устанавливаем sudo `$apt-get install sudo`.

##Настройка SSH

SSH должен работать на порту 4242. В целях безопасности должно быть невозможно подключение по SSH через root.

Править SSH конфиг тут - `$nano /etc/ssh/sshd_config`.
Указываем порт - `Port 4242`, запрещаем подкл через root - `PermitRootLogin no`. 

##Выдача root-прав пользователю

`$usermod -aG root <user>`

Сохраняем бэкап конфиги sudo `$cp /etc/sudoers /etc/sudoers.backup`. 
Исправляем конфиг sudo `$nano /etc/sudoers`: добавляем строку `<user>    ALL=(ALL:ALL) ALL` и перезагружаем `reboot`.

##Firewall

Настроить ОС с помощью брандмауэра UFW (оставить открытым только порт 4242).

Установка - `$apt-get install ufw` (проверить - `$ufw status`).
Включение - `$ufw enable` (проверить - `$ufw status`).
Дефолтные настройки - `$sudo ufw default deny incoming` и `$sudo ufw default allow outgoing`.
Разрешаем наш порт SSH - `$ufw allow 4242`.

Имя хоста - это логин + "42" (например, gmyriah42). Изменить имя машины можно с помощью:
`vim /etc/hostname`.