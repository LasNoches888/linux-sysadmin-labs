# Lab 2: systemd-сервис

## Задание
Оформить приложение как systemd-сервис с автозапуском и рестартом при сбое.
См. `units/myapp.service`.

```bash
sudo cp units/myapp.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now myapp
systemctl status myapp
```
