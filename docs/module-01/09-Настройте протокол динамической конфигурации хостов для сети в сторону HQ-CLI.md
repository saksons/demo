# Настройте протокол динамической конфигурации хостов для сети в сторону HQ-CLI

> **Настройте нужную подсеть**
> **В качестве сервера DHCP выступает маршрутизатор HQ-RTR**
> **Клиентом является машина HQ-CLI**
> **Исключите из выдачи адрес маршрутизатора**
> **Адрес шлюза по умолчанию – адрес маршрутизатора HQ-RTR**
> **Адрес DNS-сервера для машины HQ-CLI – адрес сервера HQ-SRV**
> **DNS-суффикс – au-team.irpo**
> **Сведения о настройке протокола занесите в отчёт.**


esr:esr# configure
esr:esr(config)# ip dhcp-server pool Simple
esr:esr(config-dhcp-server)# network 192.168.1.0/24
esr:esr(config-dhcp-server)# address-range 192.168.1.100-192.168.1.125
esr:esr(config-dhcp-server)# default-lease-time 1:00:00
Сконфигурируем передачу клиентам дополнительных сетевых параметров:
 маршрут по умолчанию: 192.168.1.1;
 имя домена: eltex.loc;
 список DNS-серверов: DNS1: 172.16.0.1, DNS2: 8.8.8.8.
esr:esr(config-dhcp-server)# domain-name "eltex.loc"
esr:esr(config-dhcp-server)# default-router 192.168.1.1
esr:esr(config-dhcp-server)# dns-server 172.16.0.1 8.8.8.8
esr:esr(config-dhcp-server)# exit


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