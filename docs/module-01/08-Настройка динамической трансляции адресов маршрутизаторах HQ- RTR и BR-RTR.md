# Настройка динамической трансляции адресов маршрутизаторах HQ-RTR и BR-RTR

> **Настройте динамическую трансляцию адресов для обоих офисов в сторону ISP, все устройства в офисах должны иметь доступ к сети Интернет**
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