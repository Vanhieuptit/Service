# Chuẩn bị
Chuẩn bị 2 máy chạy hệ điều hành Centos7
- Máy 1: Chạy Webserver
  - 1 CPU
  - 512M RAM
  - 2 interface  
- Máy 2: Chạy MySQL
  - 1 CPU
  - 512M RAM
  - 1 interface
# Mô hình thực hiện
![](https://imgur.com/Dee3FdB.png)
# Cấu hình
## Server 1 chạy MySQL
- Cài Mariadb
```
yum install mariadb mariadb-server -y
```
- Khởi động dịch vụ mysql
```
systemctl start mariadb
```
- Thiết lập tài khoản root quản trị cho mysql
```
mysql_secure_installation
```
- Sau đó thực hiện như sau
![](https://imgur.com/uJwdyVw.png)
- Tạo 1 database cho wordpress
  - Đăng nhập Mysql với tài khoản root `mysql -u root -p `
  - Tạo user và database sử dụng cho wordpress
```
create database wordpress;
create user 'user1'@'localhost' identified by '12345'
grant all privileges on wordpress.* to 'user1'@'192.168.50.132'; # trong đó 192.168.50.132 là địa chỉ máy chạy Webserver.
flush privileges;
exit;
```
- Bật tưởng lửa cho phép truy cập
```
firewall-cmd --add-service=mysql --permanent
firewall-cmd --reload
```
## Máy 2 chạy Webserver
### Cài Apache 
```
yum install -y httpd
```
- Bật dịch vụ webserver
```
systemctl start httpd
systemctl enable httpd
```
- Cấu hình firewall 
```
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
```
- Mở trình duyệt đánh địa chỉ ip để kiểm tra kết quả
### Cài PHP
- Cài gói EPEL
```
yum install -y epel-release
```
- Cài kho **remi**
```
rpm -Uvh https://rpms.remirepo.net/enterprise/remi-release-7.rpm
```
- Kích hoạt **yum-utils** để sử dụng **yum-config-manager**
```
yum install -y yum-utils
```
- Cài PHP7x (x là số hiệu các phiên bản)
```
yum --enablerepo=remi-php70 install php
```
- Cài thêm các options của PHP
```
yum install -y php php-mysql php-opcache
```
- Kiểm tra kết quả bằng cách tạo 1 file `info.php` vào thư mục `/var/www/html`. Thực hiện tạo nội dung cho file `info.php` 
```
echo "<?php phpinfo(); ?>" > /var/www/html/info.php
```
- Truy cập trình duyệt, nhập địa chỉ ip server kèm theo đường dẫn info.php. Nếu kết quả trả về như sau là đã thành công
### Cài wordpress
- Vào thư mục `html`
```
cd /var/www/html
```
- Tại vị trí này tải file wordpress từ internet về bằng câu lệnh
```
wget https://wordpress.org/latest.tar.gz
```
- Giải nén file vừa tải về bằng câu lênh
```
tar -xzvf latest.tar.gz
```
- Cấu hình wordpress để kết nối với MySQL
  - Di chuyển các file vừa giải nén vào trong thư mục /var/www/html. Lưu ý là thực hiện câu lệnh khi ta đang đứng tại vị trí `var/www/html`
```
mv wordpress/* /var/www/html
mv wp-config-sample.php wp-config.php
```
- Sửa các dòng sau trong file `wp-config.php` sao cho phù hợp với database vừa tạo
```
define('DB_NAME', 'wordpress');    

define('DB_USER', 'user1');    

define('DB_PASSWORD', '12345');      

define('DB_HOST', '192.168.50.2'); # trong đó 192.168.50.2 là địa chỉ của máy chạy Mysql
```
- Khởi động lại dịch vụ http
```
systemctl start httpd
```
- Truy cập trình duyệt đánh địa chỉ ip để kiểm tra kết quả

