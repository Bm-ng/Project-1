## Basic Authentication.md

- Tạo và cấu hình file auth_basic.conf
```sh
#nano /etc/httpd/conf.d/auth_basic.conf
```
- Cấu hình:
```sh
# create new
<Directory /var/www/html>
    AuthType Basic
    AuthName "Basic Authentication"
    AuthUserFile /etc/httpd/conf/.htpasswd
    require valid-user
</Directory>
```
- Tạo User:
```sh
#htpasswd -c /etc/httpd/conf/.htpasswd bmng1 
New password: # set password
Re-type new password: # confirm
Adding password for user bmng1
```
# Lưu ý: `-C` chỉ dành cho khởi tạo User ban đầu, các User sau không cần.

```sh
#htpasswd /etc/httpd/conf/.htpasswd bmng2
New password: # set password
Re-type new password: # confirm
Adding password for user bmng2
```
- Khởi động lại Httpd:
```sh
# systemctl restart httpd
```
- Kiểm tra lại kết quả.
