# Настройте протокол динамической конфигурации хостов для сети в сторону HQ-CLI

> **Настройте нужную подсеть**
> **В качестве сервера DHCP выступает маршрутизатор HQ-RTR**
> **Клиентом является машина HQ-CLI**
> **Исключите из выдачи адрес маршрутизатора**
> **Адрес шлюза по умолчанию – адрес маршрутизатора HQ-RTR**
> **Адрес DNS-сервера для машины HQ-CLI – адрес сервера HQ-SRV**
> **DNS-суффикс – au-team.irpo**
> **Сведения о настройке протокола занесите в отчёт.**


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

watch nmcli dev show
```