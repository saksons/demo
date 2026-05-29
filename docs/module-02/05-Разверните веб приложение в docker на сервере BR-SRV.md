# Разверните веб приложение в docker на сервере BR-SRV
>**Средствами	docker	должен	создаваться	стек	контейнеров	с	веб приложением и базой данных**
>**Используйте	образы	site_latest и mariadb_latest располагающиеся	в директории docker в образе Additional.iso**
>**Основной контейнер testapp должен называться tespapp**
>**Контейнер с базой данных должен называться db**
>**Импортируйте образы в docker, укажите в yaml файле параметры подключения к СУБД, имя БД - testdb, пользователь testс паролем P@ssw0rd, порт приложения 8080, при необходимости другие параметры**
>**Приложение должно быть доступно для внешних подключений через порт 8080**

```bash
# BR-SRV
dnf install docker-ce docker-ce-cli docker-compose

systemctl enable --now docker

mkdir docker_iso

mount -o loop /root/Additional.iso /root/docker_iso
# или
lsblk
mount {disk} /root/docker_iso

cd docker_iso

docker load < ./docker/site_latest.tar
docker load < ./docker/mariadb_latest.tar

cd ~/

vi docker-compose.yml
```

```yaml
networks:
  services-net:
    driver: bridge

services:
  db:
    image: mariadb:10.11
    container_name: db
    hostname: db
    restart: always
    environment:
      DB_NAME: 'testdb'
      DB_USER: 'testc'
      DB_PASS: 'P@ssw0rd'
      MARIADB_ROOT_PASSWORD: 'P@ssw0rd'
    ports:
      - "3306:3306"
    networks:
      services-net:

  testapp:
    image: site:latest
    container_name: testapp
    hostname: testapp
    restart: always
    environment:
      DB_HOST: 'db'
      DB_PORT: '3306'
      DB_NAME: 'testdb'
      DB_USER: 'testc'
      DB_PASS: 'P@ssw0rd'
      DB_TYPE: 'maria'
    ports:
      - "8080:8080"
    networks:
      services-net
    depends_on:
        db:
```

Создаём пользователя
```bash
docker-compsoe up -d
docker exec -it db mysql -u root -p

    CREATE DATABASE testdb;
    CREATE USER 'testс'@'%' IDENTIFIED BY 'P@ssw0rd';
    GRANT ALL PRIVILEGES ON testdb.* TO 'testc'@'%';
    FLUSH PRIVILEGES;
    \q
```

На клиенте через браузер проверяем
```bash
http://192.168.255.2:8080
```