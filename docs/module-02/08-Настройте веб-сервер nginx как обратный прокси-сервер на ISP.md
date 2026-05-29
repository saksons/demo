# Настройте веб-сервер nginx как обратный прокси-сервер на ISP
> **При обращении по доменному имени web.sirius-exam.org у клиента должно открываться веб приложение на HQ-SRV**
> **При	обращении	по	доменному	имени	docker.sirius-exam.org	клиента должно открываться веб приложение testapp**
```bash
dnf install nginx

vi /etc/nginx/nginx.conf
```

```diff
http {
-    server {
-            listen       80;
-            listen       [::]:80;
-            server_name  _;
-            root         /usr/share/nginx/html;
-    
-            # Load configuration files for the default server block.
-            include /etc/nginx/default.d/*.conf;
-        }
+    server {
+        listen 80;
+        server_name web.au-team.irpo;
+        location / {
+            proxy_pass http://172.16.1.2;
+            proxy_set_header Host $host;
+            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
+            proxy_set_header X-Real-IP $remote_addr;
+        }
+    }
+    server {
+        listen 80;
+        server_name docker.au-team.irpo;
+        location / {
+            proxy_pass http://172.16.2.2:8080;
+            proxy_set_header Host $host;
+            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
+            proxy_set_header X-Real-IP $remote_addr;
+        }
+    }
}

```

```bash
# Проверка конфига
nginx -t

systemctl enable --now nginx
systemctl restart nginx
````