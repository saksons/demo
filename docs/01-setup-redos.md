# ВЫРУБАЕМ ВЕЗДЕ SELINUX
```bash
vi /etc/selinux/config
```

```diff
-SELINUX=enforced
+SELINUX=disabled
```