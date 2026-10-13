# Lab 6: SSH-ключи и удалённая работа

## Задание
Перейти на вход по ключам, настроить удобные алиасы хостов и безопасно копировать файлы.

## 1. Генерация ключа
```bash
ssh-keygen -t ed25519 -C "student@lab" -f ~/.ssh/id_lab
```
Задайте парольную фразу (passphrase) — она защищает приватный ключ при краже файла.
Публичный ключ — `~/.ssh/id_lab.pub`, его можно передавать; **приватный (`id_lab`) — никогда**.

## 2. Установка ключа на сервер
```bash
ssh-copy-id -i ~/.ssh/id_lab.pub deploy@server.lab
ssh -i ~/.ssh/id_lab deploy@server.lab
```
Права важны: `~/.ssh` — 700, `authorized_keys` и приватные ключи — 600. Иначе sshd проигнорирует ключ.
```bash
chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys
```

## 3. Конфиг клиента `~/.ssh/config`
```
Host lab
    HostName server.lab
    User deploy
    IdentityFile ~/.ssh/id_lab
    IdentitiesOnly yes

Host db-internal
    HostName 10.0.0.20
    User deploy
    ProxyJump lab
```
Теперь `ssh lab` — вход на сервер, `ssh db-internal` — вход на внутренний хост через промежуточный (jump host).

## 4. ssh-agent
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_lab          # passphrase вводится один раз за сессию
ssh-add -l                     # список загруженных ключей
```

## 5. Копирование файлов
```bash
scp report.txt lab:/tmp/
rsync -avz --progress ./site/ lab:/var/www/site/     # передаёт только изменения
rsync -avz --dry-run ./site/ lab:/var/www/site/      # сначала посмотреть, что изменится
```

## 6. Порт-форвардинг
```bash
ssh -L 8080:localhost:80 lab         # локальный порт 8080 → порт 80 на сервере
```

## Проверка
- Вход выполняется без запроса пароля пользователя (запрашивается только passphrase ключа, если он не в агенте).
- `ssh -v lab` показывает строку `Authentication succeeded (publickey)`.
- После отключения `PasswordAuthentication no` вход по паролю отклоняется (см. `linux-hardening-lab`).

## Вопросы
1. Что случится при первом подключении к серверу и откуда берётся запись в `~/.ssh/known_hosts`?
2. Чем `ProxyJump` лучше проброса агента (`ForwardAgent`) с точки зрения безопасности?
3. Как отозвать доступ ключа? (подсказка: `authorized_keys`)
