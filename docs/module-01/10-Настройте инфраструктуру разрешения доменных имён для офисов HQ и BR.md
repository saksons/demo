# Настройте инфраструктуру разрешения доменных имён для офисов HQ и BR

> **Основной DNS-сервер реализован на HQ-SRV**
> **Сервер должен обеспечивать разрешение имён в сетевые адреса устройств и обратно в соответствии с таблицей 3**
> **В качестве DNS сервера пересылки используйте любой общедоступный DNS сервер(77.88.8.7, 77.88.8.3 или другие)**

```bash
dnf install bind bind-utils -y

vi /etc/named.conf
```

``diff
- listen-on port 53 { 127.0.0.1; };
+ listen-on port 53 { any; };
- allow-query { 127.0.0.1; };
+ allow-query { any; };
- recursion no;
+ recursion yes;
+ forward first;
+ forwarders { 77.88.8.8; 8.8.8.8; };


+ zone "au-team.irpo" {
+     type master;
+     file "master/au-team.irpo";
+ };
```

```bash
mkdir /var/named/master
cp /var/named/named.localhost /var/named/master/it-sirius.any
vi /var/named/master/it-sirius.any
```

```diff
- rname.invalid.
+ au-team.irpo.

+ ;NS
+ @ IN NS ns1.au-team.irpo
+ ;A
+ hq-rtr    IN A 172.16.1.2
+ br-rtr    IN A 172.16.2.2
+ hq-srv    IN A 192.168.100.2
+ hq-cli    IN A 192.168.200.2
+ br-srv    IN A 192.168.255.2
+ docker    IN A 172.16.1.1
+ web       IN A 172.16.2.1
```

```bash
chown -R root:named /var/named/master

chmod 0770 /var/named/master
chmod 0640 /var/named/master/*

systemctl enable --now named
```

# Если что-то не работает, то
```bash
named-checkconf
```