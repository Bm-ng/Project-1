# Cài đặt wordpress 

## 1. Install wget
```sh
#yum install wget unzip
```
## 2. Download WordPress
```sh
#wget http://wordpress.org/latest.zip
```
## 3. Install php-gd
```sh
#yum install php-gd
```
## 4. TạoMysql Database
```sh
#mysql -u root -p

mysql> CREATE DATABASE wordpress;
mysql> GRANT ALL PRIVILEGES on wordpress.* to 'wpuser'@'localhost' identified by 'your_password';
mysql> FLUSH PRIVILEGES;
mysql> exit
```
## 5. Khởi động lại MySQL
```sh
#service restart mysqld.service
```
## 6.Unzip và cấu hình cho Wordpress

```sh
#unzip -q latest.zip -d /var/www/html/
```
- Cấp quyền:
```sh
#chown -R apache:apache /var/www/html/wordpress
#chmod -R 755 /var/www/html/wordpress
```
- Tạo thư mục để upload
```sh
#mkdir -p /var/www/html/wordpress/wp-content/uploads
```
- Cho phép máy chủ web Apache ghi vào thư mục tải lên. Thực hiện việc này bằng cách gán quyền sở hữu nhóm của thư mục này cho máy chủ web, điều này sẽ cho phép Apache tạo các tệp và thư mục:
```sh
#chown -R :apache /var/www/html/wordpress/wp-content/uploads
```
- Vào mục Wordpress:
```sh
#cd /var/www/html/wordpress/
```
- Đổi tên  wp-config-sample.php thành  wp-config.php:
```sh
#mv wp-config-sample.php wp-config.php
```
Mở tệp cấu hình WordPress và thay đổi các giá trị cơ sở dữ liệu với các giá trị đã tạo ban đầu [4]:
```sh

# nano wp-config.php

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'wordpress');

/** MySQL database username */
define('DB_USER', 'wpuser');

/** MySQL database password */
define('DB_PASSWORD', 'your_password');

/** MySQL hostname */
define('DB_HOST', 'localhost');

```
## 7. Kiểm tra

`http: //your_ip_address/wordpress/wp-admin/install.php`

Đăng nhập và tạo các User theo yêu cầu.
