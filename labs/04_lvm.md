# Lab 4: LVM

## Задание
Освоить LVM без риска для реальных дисков: создать «диски» из файлов через loop-устройства,
собрать том, смонтировать его и расширить «на лету» без размонтирования.

> Все команды выполняются от root (`sudo`). Работайте только с созданными в лабе файлами и loop-устройствами —
> не подставляйте реальные `/dev/sdX`.

## Подготовка «дисков»
```bash
sudo truncate -s 1G /var/tmp/disk1.img /var/tmp/disk2.img
sudo losetup -f --show /var/tmp/disk1.img     # напечатает, например, /dev/loop10
sudo losetup -f --show /var/tmp/disk2.img     # например, /dev/loop11
```
Дальше `LOOP1` и `LOOP2` — имена устройств из вывода выше.

## Шаги
```bash
# 1. Физические тома и группа томов
sudo pvcreate $LOOP1 $LOOP2
sudo vgcreate vg_lab $LOOP1

# 2. Логический том, файловая система, монтирование
sudo lvcreate -L 500M -n lv_data vg_lab
sudo mkfs.ext4 /dev/vg_lab/lv_data
sudo mkdir -p /mnt/lab
sudo mount /dev/vg_lab/lv_data /mnt/lab
df -h /mnt/lab

# 3. Расширение: добавляем второй диск в группу и растим том вместе с ФС (-r)
sudo vgextend vg_lab $LOOP2
sudo lvextend -r -L +1200M /dev/vg_lab/lv_data
df -h /mnt/lab                                # размер вырос, размонтирование не потребовалось
```

## Проверка
```bash
sudo pvs ; sudo vgs ; sudo lvs
sudo lvdisplay /dev/vg_lab/lv_data
```
Ожидаемый результат: группа `vg_lab` состоит из двух физических томов, `lv_data` занимает около 1,7 ГБ,
`df` показывает увеличенный размер.

## Очистка (обязательно)
```bash
sudo umount /mnt/lab
sudo lvremove -y /dev/vg_lab/lv_data
sudo vgremove -y vg_lab
sudo pvremove -y $LOOP1 $LOOP2
sudo losetup -d $LOOP1 $LOOP2
sudo rm /var/tmp/disk1.img /var/tmp/disk2.img
```

## Вопросы
1. Чем `lvextend -L +1200M` отличается от `lvextend -L 1200M`?
2. Почему уменьшать том с ext4 опаснее, чем увеличивать? В каком порядке нужно выполнять шаги при уменьшении?
3. Для чего нужны снимки (`lvcreate -s`) и чем они опасны при долгом хранении?
