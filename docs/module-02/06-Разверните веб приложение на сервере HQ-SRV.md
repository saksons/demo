# Разверните веб приложение на сервере HQ-SRV
> **Используйте веб-сервер apache**
> **В качестве системы управления базами данных используйте mariadb**
> **Файлы веб приложения и дамп базы данных находятся в директории web образа Additional.iso**
> **Выполните импорт схемы и данных из файла dump.sql в базу данных webdb**
> **Создайте пользователя webс паролем P@ssw0rd и предоставьте ему права доступа к этой базе данных**
> **Файлы index.php и директорию images скопируйте в каталог веб сервера apache В	файле	index.php	укажите	правильные	учётные	данные	для подключения к БД**
> **Запустите веб сервер и убедитесь в работоспособности приложения**
> **Основные параметры отметьте в отчёте**

```bash
# HQ-SRV
mkdir web_iso

mount -o loop /root/Additional.iso /root/web_iso

cd web_iso

dnf install mariadb-server mariadb httpd php php-mysqlnd

systemctl enable --now mariadb

cd ~/

# Устанавливаем пароль root для mariadb . Во всех пунктах вводим Y (Yes)
mysql_secure_installation
```

```bash
mysql -u root -p

CREATE DATABASE webdb;
CREATE USER 'webc'@'%' IDENTIFIED BY 'P@ssw0rd';
GRANT ALL PRIVILEGE webdb.* TO 'webc'@'%';
FLUSH PRIVILEGES;

mysql webdb < ./web_iso/web/dump.sql
```

```bash
cp web_iso/web/index.php /var/www/html
cp web_iso/web/logo.png /var/www/html

chmod +w /var/www/html/*

systemctl enable --now httpd
systemctl restart httpd
```

На клиенте через браузер проверяем
```bash
http://192.168.100.2
```