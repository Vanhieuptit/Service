> # Thực hiện chạy wordpress trên site test.1lab.xyz đã cài ssl
# Cài PHP
Bước 1: Cài kho epel
```
yum install -y epel-release
```
Bước 2: Cài kho remi
```
rpm -Uvh https://rpms.remirepo.net/enterprise/remi-release-7.rpm
```
Bước 3: Cài yum-utils
```
yum install -y yum-utils
```
Bước 4: Cài PHP74
```
yum-config-manager --enable remi-php74 php
```
Bước 5: Cài các option hỗ trợ
```
yum install -y php-mysql php-fpm
```
### Cấu hình PHP 
Bước 1: Sửa file cấu hình
```
vi /etc/php-fpm.d/www.conf
```
- File ban đầu 

![](https://imgur.com/3OlxDs9.png)
- Sửa thành

![](https://imgur.com/xCDJHhH.png)
- Khởi động lại php-fpm
```
systemctl start php-fpm
```
- Lúc này PHP đã sẵn sàng để hoạt động
### Cấu hình Nginx để xử lý các trang PHP
- Nginx có một thư mục chuyên dụng làm nơi để xác định mỗi trang web được lưu trữ dưới dạng cấu hình riêng biệt, sử dụng khối server. Điều này tương tự với các máy chủ ảo Apache.
- Sửa một tệp mặc định dùng để định nghĩa các tệp cấu hình
```
vi /etc/nginx/conf.d/default.conf
```
- Sửa các thành như sau

![](https://imgur.com/G0Od6a3.png)
- Khởi động lại dịch vụ
```
systemctl restart nginx
```
- Kiểm tra xử lý PHP trên máy chủ của bạn bằng cách tạo một file php vào thư mục xử lý của nó. Ở đây Ngnix xử lý php ở thư mục /usr/share/nginx/html
```
echo "<?php phpinfo(); ?> > /usr/share/nginx/test.1lab.xyz
```
- Vào trình duyệt truy cập 
```
test.1lab.xyz/info.php
```
- Nếu thành công kết quả sẽ trả về
![](https://imgur.com/uSGZPw7.png)
## Cài Mysql
Bước 1: Cài đặt mariadb
```
yum install -y maria mariadb-server
```
Bước 2: Bật dịch vụ
```
systemctl start mariadb
```
Bước 3: Thiết lập bảo mật cho tài khoản root
```
mysql_secure_installation
```
- Thực hiện như sau
![](https://imgur.com/uJwdyVw.png)
Bước 3: Tạo database 
```
mysql -u root -p
```
- Nhập mật khẩu vừa tạo xong sau đó thực hiện 
```
create database newdatabase; # tạo một database có tên là newdatabase
```
```
create user 'user1'@'localhost' identified by '12345'; # tạo người dùng tên là user1 có mật khẩu là 12345
```
```
grant all privileges on newdatabase.* to 'user1'@'localhost'; # cập tất cả các quyền cho người dùng user1 đối với database newdatabase
```
```
flush privileges; # cập nhật lại thay đổi
```
```
exit
```
## Cài Wordpress
Bước 1: Tải file nén wordpress về máy
```
wget https://wordpress.org/latest.tar.gz
```
Bước 2: Giải nén file vào thư mục /usr/share/nginx/test.1lab.xyz
```
tar -xzvf latest.tar.gz -C /usr/share/nginx/test.1lab.xyz
```
Bước 3: Di chuyển các file wordpress sang /usr/share/nginx/test.1lab.xyz
```
mv wordpress/* /usr/share/nginx/test.1lab.xyz
```
Bước 4: Đổi tên file wp-config-example.php
```
mv wp-config-example.php wp-config.php
```
Bước 5: Sửa file wp-config.php như sau
![](https://imgur.com/urO2J9v.png)
Bước 6: Khởi động lại dịch vụ
```
systemctl restart nginx
```
- Check kết quả bằng cách truy cập vào địa chỉ ip server, nếu kết quả hiện thị như dưới đây là đã cài LEMP thành công
![](https://imgur.com/NaOXAxO.png)