# На маршрутизаторе ISP настройте web-based аутентификацию
> **При	обращении	к	сайту	web.sirius-exam.org	клиенту	должно	быть предложено ввести аутентификационные данные**
> **В качестве логина для аутентификации выберите WEBс паролем P@ssw0rd**
> **Выберите файл /etc/nginx/.htpasswd в качестве хранилища учётных записей**
> **При успешной аутентификации клиент должен перейти на веб сайт.**

```bash
dnf install httpd-tools

htpasswd -c /etc/nginx/.htpasswd WEBc

vi /etc/nginx/nginx.conf
```
```diff
http{
    server {
        server_name web.au-team.irpo
        location / {
+           auth_basic "admin area";
+           auth_basic_user_file /etc/nginx/.htpasswd;
        }
    }
}
```
```bash
systemctl restar nginx
```