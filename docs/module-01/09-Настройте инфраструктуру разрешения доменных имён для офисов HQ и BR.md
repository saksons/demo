# Настройте инфраструктуру разрешения доменных имён для офисов HQ и BR
> **Основной DNS-сервер реализован на HQ-SRV**
> **Сервер должен обеспечивать разрешение имён в сетевые адреса устройств и обратно в соответствии с таблицей 3**
> **В качестве DNS сервера пересылки используйте любой общедоступный DNS сервер (77.88.8.7, 77.88.8.3 или другие)**
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

# Настройка обратной зоны
```bash
vi /etc/named.conf
```
```diff
+zone "100.168.192.in-addr.arpa" {
+        type master;
+        file "master/100.168.192.in-addr.arpa";
+};
+
+zone "200.168.192.in-addr.arpa" {
+        type master;
+        file "master/200.168.192.in-addr.arpa";
+};
+
+zone "255.168.192.in-addr.arpa" {
+        type master;
+        file "master/255.168.192.in-addr.arpa";
+};
```
```bash
cp au-team.irpo /var/named/master/100.168.192.in-addr.arpa
cp au-team.irpo /var/named/master/200.168.192.in-addr.arpa
cp au-team.irpo /var/named/master/255.168.192.in-addr.arpa
```

```diff
$TTL 1d
+$ORIGIN 100.168.192.in-addr.arpa.
+@             IN      SOA  @ ns1.au-team.irpo. (
                              0          ; serial number
                              1D         ; refresh
                              1H         ; update retry
                              3W         ; expiry
                              3H         ; minimum
                              )
              IN      NS      ns1.au-team.irpo.
+2             IN      PTR     hq-srv.au-team.irpo.
```

```diff
$TTL 1d
+$ORIGIN 200.168.192.in-addr.arpa.
+@             IN      SOA  @ ns1.au-team.irpo. (
                              0          ; serial number
                              1D         ; refresh
                              1H         ; update retry
                              3W         ; expiry
                              3H         ; minimum
                              )
              IN      NS      ns1.au-team.irpo.
+2             IN      PTR     hq-cli.au-team.irpo.
```

```diff
$TTL 1d
+$ORIGIN 255.168.192.in-addr.arpa.
+@             IN      SOA  @ ns1.au-team.irpo. (
                              0          ; serial number
                              1D         ; refresh
                              1H         ; update retry
                              3W         ; expiry
                              3H         ; minimum
                              )
              IN      NS      ns1.au-team.irpo.
+2             IN      PTR     br-srv.au-team.irpo.
```

```bash
chown root:named /var/named/master/100.168.192.in-addr.arpa
chown root:named /var/named/master/200.168.192.in-addr.arpa
chown root:named /var/named/master/255.168.192.in-addr.arpa
```

## Если что-то не работает, то
```bash
named-checkconf
```


# Настройте часовой пояс на всех устройствах (за исключением виртуального коммутатора, в случае его использования) согласно месту проведения экзамена
```bash
# ISP
timedatectl set-timezone Europe/Moscow

# HQ-RTR
clock timezone gmt +3
end
commit
# BR-RTR
clock timezone gmt +3
end
commit

# HQ-SRV
timedatectl set-timezone Europe/Moscow
# HQ-CLI
timedatectl set-timezone Europe/Moscow
# BR-SRV
timedatectl set-timezone Europe/Moscow
```