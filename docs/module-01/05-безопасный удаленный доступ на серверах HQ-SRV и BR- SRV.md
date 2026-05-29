# безопасный удаленный доступ на серверах HQ-SRV и BR- SRV
> **Для подключения используйте порт 2026**
> **Разрешите подключения исключительно пользователю sshuser**
> **Ограничьте количество попыток входа до двух**
> **Настройте баннер «Authorized access only»**
```bash
echo "Authorized access only" > /etc/banner

vi /etc/ssh/sshd_config
```

```diff
- #Port 22
+ Port 2026

+ AllowUsers

- #MaxAuthTries 6
+ MaxAuthTries 2

- #Banner none
+ Banner /etc/banner
```