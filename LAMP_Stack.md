> # Linux + Apache + MySQL + PHP = LAMP
- Là một thuật ngữ được dùng để chỉ sự sử dụng các phần mềm Linux, Apache, MySQL và ngôn ngữ PHP
- Những thứ này kết hợp để tạo nên một môi trường máy chủ Web có khả năng chứa và phân phối các trang Web động.
- Ở đây Linux là một hệ điều hành
- Apache là một phần mềm máy chủ Web
- MySQL là một hệ quản trị cơ sở dữ liệu nguồn mở
- PHP là một ngôn ngữ kịch bản trên máy chủ, tương thích nhiều nền tảng hệ điều hành, hoạt động như một thành phần của Apache
## Cài đặt LAMP
### Bước 1: Tắt SElinux
- Chạy lệnh
```
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config && sentenfo
```
### Bước 2: Cài đặt Apache
- Chạy các lệnh
```
yum -y install epel-release
yum -y update
yum -y install httpd
```
- Khởi động dịch vụ http
```
systemctl start httpd
systemctl enable httpd
```
- Lúc này, ta mở trình duyệt web và gõ địa chỉ ip của server lên thì thấy vẫn chưa truy cập được vào web. Vì vậy cần phải cấu hình firewall cho phép client truy cập dịch vụ bằng câu lệnh
```
firewall-cmd --add-service=http --permanent
firewall-cmd --add-service=https --permanent
firewall-cmd --reload
```
- Vào lại trình duyệt bằng địa chỉ ip của server, ta thấy giao diện web là đã cấu hình Apache thành công.
![](https://imgur.com/8yKytdU.png)
### Cài đặt MariaDB
- Cài đặt mariadb `yum -y install mariadb-server`
- Khởi động mariadb `systemctl start mariadb`
- Cấu hình cho khởi động lại khi reboot hệ điều hành `systemctl enable mariadb`
- Mở tưởng lửa cho phép truy cập dịch vụ
```
firewall-cmd --add-service=mysql --permanent
firewall-cmd --reload
```
- Đặt mật khẩu root bằng các câu lệnh
```
mysql_secure_installation
```
Sau đó thực hiện như sau
![](https://imgur.com/uJwdyVw.png)
### Cài đặt PHP
- Ta sẽ sử dụng kho Remi để cài đặt PHP . Câu lệnh kích hoạt cả kho EPEL
```
# yum install epel-release 
```
- Phải kích hoạt kho epel thì ta mới sử dụng được  `yum-config-manager`
- Kích hoạt kho lưu trữ Remi PHP7
`yum-config-manager --enable remi-php7x`
- Trong đó x là số phiên bản PHP7 ví dụ **php73** tức là câu lệnh sẽ như sau `yum-config-manager --enable remi-php73`
- Cài các option cho PHP: `yum -y install php php-opcache php-mysql`
- Để kiểm tra kết quả, ta thêm file sau:
`echo "<?php phpinfo(); ?>" > /var/www/html/info.php`
- Sau đó Restart lại Apache: `systemctl restart httpd`
- Kiểm tra xem đã thực hiện thành công chưa bằng cách gõ địa chỉ Ip của server và thêm đường dẫn /info.php. VD: 192.168.19.130/info.php
- Nếu màn hình xuất hiện như dưới đây là đã thành công.
# Cài Wordpress
- Bước 1: Tải tập tin Wordpress từ internet về thư /var/www/html
```
wget -P /var/www/html https://wordpress.org/latest.tar.gz
```
- Bước 2: Giải nén tệp tin vừa tải về bằng câu lệnh `tar -xzvf latest.tar.gz`
- Bước 3: di chuyển các file trong wordpress sang /var/www/html bằng câu lệnh `mv wordpress/* /var/www/html`    
- Bước 4: Đổi tên file `wp-config-sample.php` thành file `wp-config.php` bằng câu lệnh `mv wp-config-sample.php wp-config.php`
- Bước 5: Truy cập cơ sở dữ liệu mariadb
  - `mysql -u root -p`
  - Tạo cơ sở dữ liệu và user có quyền sử dụng nó
  - `create database wordpress;`. Đây là câu lệnh tạo cơ sở dư liệu có tên là wordpress.
  - `create user 'user1'@'localhost' identified by 'password';`. tạo một người dùng tên là user1 với mật khẩu là password
  - `grant all privileges on wordpress.* to 'user1'@'localhost';` là câu lệnh gán quyền sử dụng database cho user
  - Cập nhật lại thay đổi `flush privileges;`
  - Thoát khỏi mysql `exit;`
Bước 6: Cấu hình file cho Wordpress kết nối với Database
`vi wp-config.php`




