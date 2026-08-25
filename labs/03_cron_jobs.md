# Lab 3: Cron

## Задания
- Каждую ночь в 02:00 — бэкап.
- Каждые 5 минут — health-check.

## crontab
```
0 2 * * *  /opt/scripts/backup.sh /data /backups
*/5 * * * * /opt/scripts/healthcheck.sh
```
