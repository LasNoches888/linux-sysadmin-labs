# linux-sysadmin-labs

Лабы по администрированию Linux: systemd, cron, пользователи, права, LVM.

## Содержание
- `labs/01_users_permissions.md`
- `labs/02_systemd_service.md`
- `labs/03_cron_jobs.md`
- `units/myapp.service` — пример unit-файла

## Дополнительно
- `labs/04_lvm.md` — LVM на loop-устройствах; `labs/05_logrotate.md` — ротация логов; `labs/06_ssh_keys.md` — SSH-ключи и ProxyJump; `labs/07_journald_troubleshooting.md` — диагностика через journalctl.
- `units/myapp-healthcheck.service` и `.timer` — периодическая проверка; `units/myapp.service.d/10-hardening.conf` — песочница для сервиса.
- `cheatsheet.md` — шпаргалка администратора.
