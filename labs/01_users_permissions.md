# Lab 1: Пользователи и права

## Задания
1. Создать пользователя `deploy` без пароля для входа по ключу.
2. Дать `deploy` право `sudo` только на `systemctl restart myapp`.
3. Настроить права 750 на `/opt/myapp`, владелец `deploy`.

## Решение (ключевые команды)
```bash
useradd -m -s /bin/bash deploy
# /etc/sudoers.d/deploy:
# deploy ALL=(root) NOPASSWD: /bin/systemctl restart myapp
install -d -m 750 -o deploy -g deploy /opt/myapp
```
