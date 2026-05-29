# Настройте сервер сетевой файловой системы (nfs) на HQ-SRV
> **В качестве папки общего доступа выберите /raid/nfs, доступ для чтения и записи исключительно для сети в сторону HQ-CLI**
> **На HQ-CLI настройте автомонтирование в папку /mnt/nfs**
> **Основные параметры сервера отметьте в отчёте**
```bash
# HQ-SRV
dnf install nfs4-acl-tools nfs-utils

mkdir /raid/nfs

chmod 777 /raid/nfs

echo -e "/raid/nfs\t192.168.200.0/28(rw,no_root_squash)" | tee -a /etc/exports

systemctl enable --now nfs-server.service
systemctl restart nfs-server.service

exports -arv

# HQ-CLI
dnf install nfs-utils
mkdir /mnt/nfs
echo -e "192.168.100.2:/raid/nfs\t/mnt/nfs\t/defaults\t0 2" | tee -a /etc/fstab
reboot
```