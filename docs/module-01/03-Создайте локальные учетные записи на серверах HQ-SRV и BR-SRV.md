# Создайте локальные учетные записи на серверах HQ-SRV и BR-SRV:

## Создайте пользователя sshuser

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


## Создайте пользователя net_admin на маршрутизаторах HQ-RTR и BR-RTR

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