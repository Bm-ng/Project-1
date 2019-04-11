## Nội dung: 
- Trong hệ thống mạng thông tin, reverse proxy là một loại proxy server trung gian giữa một máy chủ và các clients gởi tới các yêu cầu. Nó kiểm soát yêu cầu của các clients, nếu hợp lệ, sẽ luân chuyển đến các servers thích ứng.[1] Trái ngược với một proxy chuyển tiếp (forward proxy), là một trung gian cho các clients liên hệ với nó liên lạc với bất kỳ máy chủ nào, Reverse proxy là một trung gian cho các máy chủ liên hệ với nó được liên lạc bởi bất kỳ clients nào.

- Ưu điểm lớn nhất của việc sử dụng Reverse proxy là khả năng quản lý tập trung. Nó giúp kiểm soát mọi requests do clients gửi lên các servers mà được bảo vệ.
- Reverse proxy có thể che giấu sự tồn tại và các đặc điểm của các servers thực sự được dùng.
- Trong trường hợp các trang giao thức bảo mật HTTPS, một máy chủ mạng có thể không tự mã hóa SSL hay TLS, mà giao nhiệm vụ này cho reverse proxy mà có thể được trang bị phần cứng ssl accelerator card.
- Load balancing, proxy có thể chia đều các yêu cầu của các máy khách tới các servers.
- Proxy có thể nén nội dung, làm cho việc truy cập trở nên nhanh chóng.
- Proxy có thể được dùng như là một application firewall để chống đỡ các cuộc tấn công (như Tấn công từ chối dịch vụ) vào các ứng dụng web.
- Spoon feeding: một trang mạng động có thể được tạo ra bởi máy chủ mạng, proxy caching nội dung web server gửi và "rót" từ từ đến các máy khách hoạt động chậm. Máy chủ mạng không phải đợi máy khách.[2]
- Nhằm giúp giảm tải máy chủ mạng proxy có thể cache các nội dung tĩnh như hình ảnh, tập tin.

## 1.Cài đặt apache:
- Cài đặt apache trên Centos7:
```sh
#yum install httpd httpd-devel
``` 
## 2. Cấu hình Reverse Proxy trên Apache
– Chỉnh cổng mặc định 80 của Apache thành 8080
```sh
#nano /etc/httpd/conf/httpd.conf
```
Tìm dòng `Listen 80` và thay bằng `Listen 8080`
– Sau đó, di chuyển đến cuối file và dán những dòng sau vào để tạo mới một virtual host:
```sh

NameVirtualHost *:8080
 
<VirtualHost *:8080>
   ServerName bmng.com
   ServerAlias www.bmng.com
   DocumentRoot /var/www/html
       <Directory "/var/www/html">
               Options FollowSymLinks -Includes
               AllowOverride All
               Order allow,deny
               Allow from all
       </Directory>
       RewriteEngine on
</VirtualHost>
```
– Lưu lại file cấu hình và khởi động lại Apache:
```sh
#service httpd restart
```
## 3. Cài đặt Nginx
– Thêm repo EPEL
```sh
#yum install epel-release
```
- Cài đặt Nginx:
```sh
#yum install nginx
```
## 4. Cấu hình Reverse Proxy trên Nginx
– Tạo Nginx virtual host
```sh
# nano /etc/nginx/conf.d/bmng.com.conf
```
Cấu hình: 
```sh
server {
   listen 80;
   server_name bmng.com;
   access_log off;
   error_log off;

   location / {
      client_max_body_size 10m;
      client_body_buffer_size 128k;
 
      proxy_send_timeout 90;
      proxy_read_timeout 90;
      proxy_buffer_size 128k;
      proxy_buffers 4 256k;
      proxy_busy_buffers_size 256k;
      proxy_temp_file_write_size 256k;
      proxy_connect_timeout 30s;
 
      proxy_redirect http://www.bmng.com:8080 http://www.bmng.com;
      proxy_redirect http://bmng.com:8080 http://bmng.com;
 
      proxy_pass http://127.0.0.1:8080/;
 
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
   }

   # Select files to be deserved by nginx
   location ~* ^.+\.(jpg|jpeg|gif|css|png|js|ico|txt|srt|swf|zip|rar|html|htm|pdf)$ {
      root /var/www/html;
      expires 30d; # caching, expire after 30 days
   }
}
```

- Kiểm tra và khởi động lại Nginx:

```sh
#nginx -t
#service nginx restart
```

## 5.Cài đặt module Reverse Proxy And Forward cho Apache
Để Apache có thể hiểu được traffic đang đến từ đâu, chúng ta cần cài thêm module rpaf (Reverse Proxy And Forward). rpaf sẽ nhận X-Forwarded-For header từ proxy server và điều chỉnh lại remote address của client.

Cài đặt httpd-devel và gcc để compile source:
```sh
#yum install httpd-devel gcc
```
Tải về mod_rpaf:
```sh
#wget http://mirror.trouble-free.net/sources/mod_rpaf-0.6.tar.gz
#zxvf mod_rpaf-0.6.tar.gz
#cd mod_rpaf-0.6
#apxs -i -c -n mod_rpaf-2.0.so mod_rpaf-2.0.c
```
Khi thành công sẽ có thông báo : 
[imgu]
- Tạo file cấu hình cho mod_rpaf
```sh
#nano /etc/httpd/conf.d/mod_rpaf.conf
```
Cấu hình: 
```sh
LoadModule rpaf_module modules/mod_rpaf-2.0.so

# mod_rpaf Configuration

RPAFenable On
RPAFsethostname On
RPAFproxy_ips 10.10.10.12
RPAFheader X-Forwarded-For
``` 
Khởi động lại Apache
```sh
#service httpd restart
```

## 6. Kiểm tra lại kết quả



## 7. Cài đặt Mariadb:

```sh
# yum install MariaDB-server MariaDB-client -y
# systemctl start mariadb
# systemctl enable mariadb
# systemctl status mariadb
```
## 8. Cài đặt PHP

```sh
#yum install php php-mysql
```
- Khởi động lại httpd

```sh
$ sudo systemctl restart httpd.service
```




















