# Tổng quan về một Website
- Một website thông thường nó sẽ bao gồm các thành phần sau:
  - Giao diện (Front-end): là những gì chúng ta thấy nó hiển thị ra bên ngoài như bố cục, màu sắc của websit
  - Mã nguồn xử lý (Backend): đây là hệ thống xử lý, lưu trữ các thông tin mã nguồn, tập tin của website
  - Cơ sở dữ liệu (Database): là nơi lưu trữ lại các dữ liệu mềm của web như là các bài viết, nội dung trên web site, hay các thiết lập,... 
# Web động là gì?
- Trước khi đi vào tìm hiểu tiếp theo, ta cần hiểu về Web động.
- Web động (Dynamic Website) là thuật ngữ chuyên ngành dùng để chỉ những trang web được hỗ trợ bởi một phần mềm cơ sở web
- Web động được thiết kế có thêm mục truy suất dữ liệu và phần xử lý thông tin.
- Các thông tin được hiển thị trên web động được gọi là cơ sở dữ liệu (là một thành phần mà ta sẽ nói đến ở LAMP)
> # Linux + Apache + MySQL + PHP = LAMP
- Là một thuật ngữ được dùng để chỉ sự sử dụng các phần mềm Linux, Apache, MySQL và ngôn ngữ PHP
- Những thứ này kết hợp để tạo nên một môi trường máy chủ Web có khả năng chứa và phân phối các trang Web động.
- Ở đây **Linux** là một hệ điều hành
- **Apache **là một phần mềm máy chủ Web
- **MySQL** là một hệ quản trị cơ sở dữ liệu nguồn mở
- **PHP** là một ngôn ngữ kịch bản trên máy chủ, tương thích nhiều nền tảng hệ điều hành, hoạt động như một thành phần của Apache
## Cài đặt LAMP
### Bước 1: Tắt SElinux
- Chạy lệnh
```
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config && sentenfo
```
### Bước 2: Cài đặt Apache
- Các bước thực hiện đã được nêu chi tiết trước đó [tại đây](Webserver.md)
### Cài đặt MariaDB
- Cơ sở dữ liệu là một tập hợp các liên kết dữ liệu, lưu trên một thiết bị lưu trữ, được duy trì dưới dạng một tập hợp các tập tin trong hệ điều hành (hay trong các hệ quản trị cơ sở dữ liệu). 
- MariaDB chính là một hệ quản trị cơ sở dữ liệu. Ngoài ra còn có các hệ quản trị cơ sở dữ liệu phổ biến khác như MySQL, Oracle, PostgreSQL, SQL Server.
- Cài đặt mariadb `yum -y install mariadb-server`
- Khởi động mariadb `systemctl start mariadb`
- Cấu hình cho khởi động lại khi reboot hệ điều hành `systemctl enable mariadb`
- Mở tưởng lửa cho phép truy cập dịch vụ
```
firewall-cmd --add-service=mysql --permanent
firewall-cmd --reload
```
- Một user root có toàn quyền truy cập tới database và bảng trong databases. Vì vậy ta cần đặt mật khẩu root của cơ sở dữ liệu bằng các câu lệnh
- 
```
mysql_secure_installation
```
Sau đó thực hiện như sau
![](https://imgur.com/uJwdyVw.png)
### Cài đặt PHP
Bước 1:Ta sẽ sử dụng kho EPEL để cài đặt PHP.
- Kho EPEL ở đây được hiểu là một kho chứa chương trình cộng đồng mở và miễn phí được Fedora phát triển và duy trì. EPEL cung cấp 100% gói chương trình chất lượng cao dành cho các hệ điều hành Linux như Redhat, CentOS, Fedora,... và PHP là một trong số các chương trình nằm trong đó
- Câu lệnh kích hoạt cả kho EPEL
```
# yum install epel-release 
```
Bước 2: Phải kích hoạt **yum-utils** thì ta mới sử dụng được  **yum-config-manager**. Để cài **yum-utils** ta sử dụng câu lệnh `yum -y install yum-utils`
Bước 3: Cài thêm kho Remi Centos
`rpm -Uvh https://rpms.remirepo.net/enterprise/remi-release-7.rpm`
Bước 4: Cài PHP7 với câu lệnh
`yum-config-manager --enable remi-php7x`
- Trong đó x là số phiên bản PHP7 ví dụ **php73** tức là câu lệnh sẽ như sau `yum-config-manager --enable remi-php73`
- Cài các option cho PHP: `yum -y install php php-opcache php-mysql`. Câu lệnh này là cài **php opcache** và **php-mysql**.
- Để kiểm tra kết quả, ta thêm file sau:
`echo "<?php phpinfo(); ?>" > /var/www/html/info.php`
- Sau đó Restart lại Apache: `systemctl restart httpd`
- Kiểm tra xem đã thực hiện thành công chưa bằng cách gõ địa chỉ Ip của server và thêm đường dẫn /info.php. VD: 192.168.19.130/info.php
- Nếu màn hình xuất hiện như dưới đây là đã thành công.
# Cài Wordpress
- Wordpress là là một phần mềm mã nguồn mở, được viết bằng ngôn ngữ lập trình website PHP và sử dụng hệ quản trị cơ sở dữ liệu MySQL
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
  - `create database wordpress;`. Đây là câu lệnh tạo cơ sở dư liệu có tên là **wordpress**.
  - `create user 'user1'@'localhost' identified by '12345';`. tạo một người dùng tên là **user1** với mật khẩu là **12345**
  - Tại thời điểm này, user mới không có quyền làm bất cứ điều gì với cơ sở dữ liệu. Trên thực tế, ngay cả user mới cố gắng đăng nhập, họ sẽ không thể truy cập vào MySQL shell. Vì vậy cần phải cấp quyền cho user bằng câu lệnh `grant all privileges on wordpress.* to 'user1'@'localhost';`
  - Cập nhật lại thay đổi `flush privileges;`. Câu lệnh này sẽ cho phép thực hiện thay đổi ngay lập tức.
  - Thoát khỏi mysql `exit;`
  - Để hiểu rõ hơn cách thức sử dụng của các câu lệnh, hãy [vào đây](MySQL_command.md)
- Bước 6: Cấu hình file cho Wordpress kết nối với Database
`vi wp-config.php`
![](https://imgur.com/uzP8cI1.png)
- Sửa các dòng đã khoanh đỏ ở trên thành như sau
```
define('DB_NAME', 'wordpress');     # tên database tạo ở trên

define('DB_USER', 'user1');     # tên user vừa tạo

define('DB_PASSWORD', '12345');      # 12345 là password của user bên trên
```



