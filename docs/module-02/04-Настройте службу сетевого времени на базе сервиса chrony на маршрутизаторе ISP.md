# Настройте службу сетевого времени на базе сервиса chrony на маршрутизаторе ISP
> **Вышестоящий сервер ntp на маршрутизаторе ISP - на выбор участника**
> **Стратум сервера - 5**
> **В качестве клиентов ntp настройте: HQ-SRV, HQ-CLI, BR-RTR, BR-SRV.**
```bash
# ISP
vi /etc/chrony.conf
```

```diff
+ server 0.ru.pool.ntp.org iburst
+ local stratum 5
+ allow 0.0.0.0/0
```

На клиенте в /etc/chrony.conf
```diff
# HQ
+server 172.16.1.1 iburst
# BR
+server 172.16.2.1 iburst
```

```bash
# ISP + SRVs + CLI
systemctl enable --now chronyd
systemctl restart chronyd

# BR-RTR
ntp server 172.16.2.1
no ntp broadcast-client enable
end
commit
confirm
sh ntp peers
```