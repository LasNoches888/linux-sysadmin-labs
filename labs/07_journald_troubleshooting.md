# Lab 7: Журналы и диагностика systemd-сервисов

## Задание
Научиться находить причину падения сервиса с помощью `systemctl` и `journalctl`.

## Базовые команды

| Задача | Команда |
|--------|---------|
| Состояние сервиса | `systemctl status myapp` |
| Все упавшие юниты | `systemctl --failed` |
| Логи сервиса | `journalctl -u myapp` |
| Следить за логами | `journalctl -u myapp -f` |
| Только с текущей загрузки | `journalctl -b -u myapp` |
| За период | `journalctl -u myapp --since "1 hour ago"` |
| Только ошибки и выше | `journalctl -p err -b` |
| Последние N строк | `journalctl -u myapp -n 50 --no-pager` |
| Размер журнала | `journalctl --disk-usage` |
| Очистить старше недели | `sudo journalctl --vacuum-time=7d` |

## Сценарий: сервис не стартует

1. Сломайте unit намеренно: в `/etc/systemd/system/myapp.service` укажите несуществующий `ExecStart=/opt/myapp/nope.sh`.
2. Примените и запустите:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start myapp
   ```
3. Найдите причину:
   ```bash
   systemctl status myapp          # смотрим Active: failed и Main PID / status
   journalctl -u myapp -n 20       # ищем "Failed to execute" / "No such file or directory"
   ```
4. Исправьте путь, выполните `daemon-reload`, затем `systemctl reset-failed myapp` и снова `start`.

## Сценарий: бесконечные рестарты

Если сервис падает чаще, чем позволяет `StartLimitBurst` (по умолчанию 5 раз за 10 секунд),
systemd перестаёт его перезапускать: статус `start-limit-hit`.
```bash
systemctl status myapp
sudo systemctl reset-failed myapp      # сбросить счётчик
sudo systemctl start myapp
```
Причину ищите в `journalctl -u myapp`, а не в самом факте остановки.

## Постоянное хранение журнала

По умолчанию на многих системах журнал хранится в памяти и пропадает после перезагрузки.
```bash
sudo mkdir -p /var/log/journal
sudo sed -i 's/^#\?Storage=.*/Storage=persistent/' /etc/systemd/journald.conf
sudo systemctl restart systemd-journald
journalctl --list-boots               # теперь видны предыдущие загрузки
```

## Скорость загрузки
```bash
systemd-analyze                       # общее время загрузки
systemd-analyze blame | head          # самые медленные юниты
```

## Вопросы
1. В чём разница между `systemctl stop` и `systemctl disable`?
2. Что покажет `journalctl -k` и когда это полезно?
3. Как увидеть логи сервиса, который был удалён, но чей журнал остался? (подсказка: `journalctl _SYSTEMD_UNIT=…`)
