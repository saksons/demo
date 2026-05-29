# Сконфигурируйте файловое хранилище на сервере HQ-SRV
> **При помощи двух подключенных к серверу дополнительных дисков размером 1 Гб сконфигурируйте дисковый массив уровня 0**
> **Имя устройства – md0, при необходимости конфигурация массива размещается в файле /etc/mdadm.conf**
> **Создайте раздел, отформатируйте раздел, в качестве файловой системы используйте ext4**
> **Обеспечьте автоматическое монтирование в папку /raid**

```bash
# Смотрим какие есть устройства
lsblk

dnf install mdadm

mdadm --create /dev/md0 --level=0 --raid-devices=2 /dev/sdb /dev/dbc

mkfs.ext4 /dev/md0
mkdir -p /raid
mount /dev/md0 /raid

echo -e "/dev/md0\t/raid\text4\tdefaults\t0 2" | tee -a /etc/fstab
```

# Если не будет дисков by Denis Sharapov
```bash
dd if=/dev/zero of=/root/disk1.img bs=1M count=1024
dd if=/dev/zero of=/root/disk2.img bs=1M count=1024

losetup -f /root/disk1.img
losetup -f /root/disk2.img


lsblk

mdadm --create /dev/md0 --level=0 --raid-devices=2 /dev/loop0 /dev/loop1

mkfs.ext4 /dev/md0
mkdir -p /raid
mount /dev/md0 /raid

echo -e "/dev/md0\t/raid\text4\tdefaults\t0 2" | tee -a /etc/fstab
```

# С ИИшки
```bash
# Создаём резервную копию, если файл существует
[ -f /etc/mdadm.conf ] && cp /etc/mdadm.conf /etc/mdadm.conf.bak

# Генерируем конфигурацию автоматически
mdadm --detail --scan --verbose >> /etc/mdadm.conf

# Проверяем содержимое
cat /etc/mdadm.conf
```