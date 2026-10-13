# Lab 5: logrotate

## Задание
Настроить ротацию логов приложения `myapp` (из лабы 2): ежедневно, хранить 14 архивов, сжимать, не терять записи.

## Конфигурация `/etc/logrotate.d/myapp`
```
/var/log/myapp/*.log {
    daily
    rotate 14
    compress
    delaycompress
    missingok
    notifempty
    create 0640 deploy deploy
    sharedscripts
    postrotate
        systemctl kill -s HUP myapp.service >/dev/null 2>&1 || true
    endscript
}
```

| Директива | Зачем |
|-----------|-------|
| `daily` / `rotate 14` | ротация раз в сутки, хранить 14 предыдущих файлов |
| `compress` / `delaycompress` | сжимать архивы, но последний не сжимать сразу (приложение может ещё писать в него) |
| `missingok` / `notifempty` | не падать, если лога нет; не ротировать пустой файл |
| `create 0640 deploy deploy` | создать новый файл с нужными правами и владельцем |
| `postrotate … endscript` | сообщить приложению, что надо переоткрыть лог-файл |

## Проверка
```bash
sudo logrotate -d /etc/logrotate.d/myapp     # dry-run: покажет, что будет сделано, ничего не меняя
sudo logrotate -f /etc/logrotate.d/myapp     # принудительная ротация
ls -l /var/log/myapp/
cat /var/lib/logrotate/status                # когда каждый лог ротировался в последний раз
```
Ожидаемый результат: рядом с `app.log` появился `app.log.1` (без сжатия, из-за `delaycompress`),
новый `app.log` создан с правами 0640 и владельцем `deploy`.

## Если приложение не умеет переоткрывать лог по сигналу
Используйте `copytruncate`: logrotate копирует файл и обнуляет оригинал. Минус — возможна потеря
нескольких строк, записанных между копированием и обнулением.

## Вопросы
1. Что произойдёт без `postrotate`, если приложение держит файл открытым? Куда оно продолжит писать?
2. Как ограничить ротацию по размеру, а не по времени (`size 100M`)? Чем это отличается от `maxsize`?
3. Где системный таймер, запускающий logrotate? (`systemctl list-timers | grep logrotate`)
