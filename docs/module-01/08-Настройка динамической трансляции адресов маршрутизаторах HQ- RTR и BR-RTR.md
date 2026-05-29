# Настройка динамической трансляции адресов маршрутизаторах HQ- RTR и BR-RTR

## Настройте динамическую трансляцию адресов для обоих офисов в сторону ISP, все устройства в офисах должны иметь доступ к сети Интернет
```bash
# HQ-RTR
# Создаём секюр зоны
security zone UNTRUNST # to ISP for wan
exit
security zone TRUNST # TO hq-srv and hq-cli for lan
exit

# Настраиваем зоны на интерфейсах
int gi1/0/x # to ISP
security zone UNTRUST
exit
int gi1/0/y # to HQ-SRV
security zone TRUST
exit
int gi1/0/z # to HQ-CLI
security zone TRUST
exit

# Создаём профиля адресов
object-group network LOCAL_NET
ip address-range 192.168.100.1-192.168.100.30
ip address-range 192.168.200.1-192.168.200.14
exit
object-group network WAN_NET
ip address-range 172.16.1.2
exit

# хз
security zone-pair TRUST UNTRUST
rule 1
match source-address object-group LOCAL_NET
action permit
enable
exit

# Настраиваем снат
nat source
pool WAN_ADDRESS
ip address-range 172.16.1.2
exit
# Создаём набор правил снат
ruleset SNAT
to zone UNTRUST
rule 1
match source-address object-group LOCAL_NET
action source-nat pool WAN_ADDRESS
enable
end
commit
confirm


# BR-RTR
security zone UNTRUST
exit
security zone TRUST
exit

int gi1/0/a # to ISP
security zone UNTRUST
exit
int gi1/0/b # to BR-SRV
security zone TRUST
exit

object-group network LOCAL_NET # -
ip address-range 192.168.255.1-192.168.255.14
exit
object-group network WAN_NET
ip address-range 172.16.2.2
exit

security zone-pair TRUST UNTRUST # -
rule 1
match source-address object-group LOCAL_NET # -
action permit # -
enable

nat source
pool WAN_ADDRESS # -
ip address-range 172.16.2.2
exit
ruleset SNAT # -
to zone UNTRUST # ЗАБЫЛ ПОЛНОСТЬЮ
match source-address object-group LOCAL_NET # -
action source-nat pool WAN_ADDRESS # -
enable
end
commit
confirm
```

## Настройте протокол динамической конфигурации хостов для сети в сторону HQ-CLI:
> **Настройте нужную подсеть**
> **В качестве сервера DHCP выступает маршрутизатор HQ-RTR**
> **Клиентом является машина HQ-CLI**
> **Исключите из выдачи адрес маршрутизатора**
> **Адрес шлюза по умолчанию – адрес маршрутизатора HQ-RTR**
> **Адрес DNS-сервера для машины HQ-CLI – адрес сервера HQ-SRV**
> **DNS-суффикс – sirius-exam.org**
> **Сведения о настройке протокола занесите в отчёт**
```bash
# HQ-RTR
ip dhcp-server pool client
network 192.168.200.0/28
address-range 192.168.200.1-192.168.200.14
domain-name "au-team.irpo"
default-router 192.168.200.1
dns-server 192.168.100.2,77.88.8.8
end
commit
confirm

# HQ-CLI
nmcli con mod {int_name} -ipv4.method manul -ipv4.address 192.168.200.2 ipv4.method auto
nmcli con up {int_name}

nmcli dev show
ip -br a
```