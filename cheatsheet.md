# Шпаргалка администратора Linux

## Пользователи и права
| Задача | Команда |
|--------|---------|
| Создать пользователя | `useradd -m -s /bin/bash name` |
| Добавить в группу | `usermod -aG group name` |
| Заблокировать / разблокировать | `usermod -L name` / `usermod -U name` |
| Права файла | `chmod 640 file`, `chown user:group file` |
| Кто в группе sudo | `getent group sudo` |
| Проверить sudo-права | `sudo -l -U name` |
| Безопасно править sudoers | `visudo -f /etc/sudoers.d/name` |

## Процессы и службы
| Задача | Команда |
|--------|---------|
| Статус / запуск / остановка | `systemctl status\|start\|stop myapp` |
| Автозапуск вкл / выкл | `systemctl enable --now myapp` / `systemctl disable myapp` |
| Перечитать unit-файлы | `systemctl daemon-reload` |
| Таймеры | `systemctl list-timers` |
| Нагрузка по процессам | `top`, `htop`, `ps aux --sort=-%mem \| head` |

## Диски и файловые системы
| Задача | Команда |
|--------|---------|
| Место на разделах | `df -h` |
| Размер каталога | `du -sh /var/* \| sort -h` |
| Блочные устройства | `lsblk -f` |
| Монтирование | `mount`, `findmnt`, `/etc/fstab` |
| LVM | `pvs`, `vgs`, `lvs` |

## Сеть
| Задача | Команда |
|--------|---------|
| Адреса и интерфейсы | `ip -br a` |
| Маршруты | `ip route` |
| Открытые порты | `ss -tulnp` |
| DNS-запрос | `dig example.com`, `getent hosts example.com` |
| Проверка HTTP | `curl -I https://example.com` |

## Логи и диагностика
| Задача | Команда |
|--------|---------|
| Журнал сервиса | `journalctl -u myapp -f` |
| Ошибки с загрузки | `journalctl -p err -b` |
| Сообщения ядра | `dmesg -T \| tail` |
| Кто залогинен | `who`, `last -n 10` |
| Нагрузка | `uptime`, `free -h`, `vmstat 1 5` |

## Пакеты
| Дистрибутив | Установка | Обновление |
|-------------|-----------|-----------|
| Debian / Ubuntu | `apt install pkg` | `apt update && apt upgrade` |
| RHEL / Fedora | `dnf install pkg` | `dnf upgrade` |
| Alpine | `apk add pkg` | `apk update && apk upgrade` |
