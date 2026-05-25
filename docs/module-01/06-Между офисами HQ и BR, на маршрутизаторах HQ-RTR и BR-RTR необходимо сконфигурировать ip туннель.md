# Между офисами HQ и BR, на маршрутизаторах HQ-RTR и BR-RTR
необходимо сконфигурировать ip туннель
> **На выбор технологии GRE или IP in IP**
```bash
# HQ-RTR
tunnel GRE 10
ip firewall disable
local address 172.16.1.2
remote address 172.16.2.2
ip address 10.10.10.1/28
enable
exit
ip route 192.168.255.0/28 tunnel gre 10
end
commit
confirm

# BR-RTR
tunnel GRE 10
ip firewall disable
local address 172.16.2.2
remote address 172.16.1.2
ip address 10.10.10.2/28
enable
exit
ip route 192.168.100.0/28 tunnel gre 10
ip route 192.168.200.0/28 tunnel gre 10
end
commit
confirm


> **Сведения о туннеле занесите в отчёт.**