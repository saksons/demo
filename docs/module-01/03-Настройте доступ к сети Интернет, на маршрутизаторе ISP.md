# Настройте доступ к сети Интернет, на маршрутизаторе ISP
## Настройте адресацию на интерфейсах:
>**Интерфейс, подключенный к магистральному провайдеру, получает адрес по DHCP**
```bash
# ISP
nmcli con show
nmcli con mod {con_name} ipv4.method auto
nmcli con up {con_name}
```


>**Настройте маршрут по умолчанию, если это необходимо**
```bash
# ISP
nmcli con show
nmcli con mod {con_name} ipv4.gateway {address}
nmcli con up {con_name}
```


>**Настройте интерфейс, в сторону HQ-RTR, интерфейс подключен к сети 172.16.1.0/28**
```bash
# ISP
nmcli con show
nmcli con mod {con_name} ipv4.method manual ipv4.address 172.16.1.1/28 ipv4.gateway 172.16.1.1 # to hq-rtr
nmcli con up {con_name}

# HQ-RTR
configure
interface gi1/0/x # to isp
ip firewall disable
ip address 172.16.1.2/28
exit
exit
ip route 0.0.0.0/0 172.16.1.1
commit
confirm
```


>**Настройте интерфейс, в сторону BR-RTR, интерфейс подключен к сети 172.16.2.0/28**
```bash
# ISP
nmcli con show
nmcli con mod {con_name} ipv4.method manual ipv4.address 172.16.2.1/28 ipv4.gateway 172.16.2.1 ipv4.dns "77.88.8.8" # to br-rtr
nmcli con up {con_name}

# BR-RTR
configure
interface gi1/0/y # to isp
ip firewall disable
ip address 172.16.2.2/28
exit
exit
ip route 0.0.0.0/0 172.16.2.1
commit
confirm
```


## На ISP настройте динамическую сетевую трансляцию портов для доступа к сети Интернет HQ-RTR и BR-RTR.
```bash
# ISP
echo  "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
sysctl -p

dnf install iptables-services

# PREROUTING -> INPUT -> FORWARD -> OUTPUT -> POSTROUTING
#                        drop удалить         добавить 2 правила
iptables -t <таблица> <действие> <цепочка> [критерии] -j <действие_с_пакетом>

iptables -t nat -A POSTROUTING -s 172.16.1.0/28 -o ens18 -j MASQUERADE
iptables -t nat -A POSTROUTING -s 172.16.2.0/28 -o ens18 -j MASQUERADE

iptables -D FORWARD 1

iptables -L INPUT -nv --line-numbers
iptables -D INPUT {reject_num} # 5 номер

iptables-save > /etc/sysconfig/iptables
```

## Создайте локальные учетные записи на серверах HQ-SRV и BR-SRV:
> **Создайте пользователя sshuser**
> **Пароль пользователя sshuser с паролем P@ssw0rd**
> **Идентификатор пользователя 2026**
> **Пользователь sshuser должен иметь возможность запускать sudo без ввода пароля**
```bash
# HQ-SRV
useradd -m -s /bin/bash -u 2026 -U sshuser
passwd sshuser
visudo

# BR-SRV
useradd -m -s /bin/bash -u 2026 -U sshuser
passwd sshuser
visudo
```

## Создайте пользователя net_admin на маршрутизаторах HQ-RTR и BR- RTR
> **Пароль пользователя net_admin с паролем P@ssw0rd**
> **При настройке ОС на базе Linux, запускать sudo без ввода пароля**
> **При настройке ОС отличных от Linux пользователь должен обладать максимальными привилегиями.**
```bash
# HQ-RTR
username net_admin
password P@ssw0rd
privilege 15
end
commit
confirm

# BR-RTR
username net_admin
password P@ssw0rd
privilege 15
end
commit
confirm
```