# Обеспечьте динамическую маршрутизацию на маршрутизаторах HQ-RTR и BR-RTR
## сети одного офиса должны быть доступны из другого офиса и наоборот. Для обеспечения динамической маршрутизации используйте link state протокол на усмотрение участника

> **Разрешите выбранный протокол только на интерфейсах ip туннеля**
```bash
# HR-RTR
router ospf 1
router-id 10.10.10.1
area 0.0.0.0
network 192.168.100/27
network 192.168.200/28
network 10.10.10.10/28
enable
exit
enable
exit

tunnel gre 10
ip ospf instance 1
ip ospf
end
commit
confirm

# BR-RTR
routing ospf 1
router-id 10.10.10.2
area 0.0.0.0
network 192.168.255.0/28
network 10.10.10.0/28
enable
exit
enable
exit

tunnel gre 10
ip ospf instance 1
ip ospf
end
commit
confirm
```

> **Маршрутизаторы должны делиться маршрутами только друг с другом**
> **Обеспечьте защиту выбранного протокола посредством парольной защиты**
```bash
# HQ-RTR
key-chain auth_ospf
key 1
key-string ascii-text 123123123

tunnel gre 10
ip ospf authentication key-chain ospf
ip ospf authentication algorithm md5
end
commit
confirm

# BR-RTR
key-chain auth_ospf
key 1
key-string ascii-text 123123123

tunnel gre 10
ip ospf authentication key-chain ospf
ip ospf authentication algorithm md5
end
commit
confirm

```
> **Сведения о настройке и защите протокола занесите в отчёт**