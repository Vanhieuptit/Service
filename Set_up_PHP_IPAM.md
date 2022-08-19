# 1. Tổng quan
- Quản trị viên mạng / hệ thống điển hình chịu trách nhiệm quản lý một hoặc nhiều mạng con trong mạng được kiểm soát. Ví dụ, khi một phân đoạn mạng LAN được gán mạng cho /24, tổng số địa chỉ IP là 254 địa chỉ có thể được sử dụng cho các mục đích khác nhau. 
- Để theo dõi địa chỉ IP nào được gán cho máy chủ nào, cần có một dữ liệu quản lý. Các dễ nhất để làm điều đó là duy trì một bảng tính ghi lại thông phân bổ địa chỉ IP. Cách làm này thì phù hợp cho một mạng nhỏ chỉ có 1 quản trị viên. Tuy nhiên, việc dựa vào một bảng dữ liệu có sẵn không thuận tiện và dễ xảy ra lỗi với nhiều mạng lớn. 
- Một cách để quản lý phân bổ địa chỉ IP có hệ thống hơn là sử dụng công cụ quản lý địa chỉ IP dựa trên Web. Không chỉ có thể truy cập công cụ dựa trên web từ mọi nơi mà cơ sở dữ liệu phụ trợ còn đảm bảo rằng tất cả các cập nhật cho cơ sở dữ liệu được đồng bộ hóa và áp dụng đúng cách trong thời gian thực. 
- PhpIPAM là một trong những phần mềm mã nguồn mở quản lý địa chỉ IP tốt nhất được sử dụng bởi một số dịch vụ web hosting tốt nhất. Đây là một ứng dụng dựa trên các tính năng của PHP, cung cấp giao diện web hiện đại và hữu ích để quản lý địa chỉ IP. Vì vậy, đây là một lựa chọn lý tưởng cho cả người mới cũng như quản trị viên website đã có kinh nghiệm.
# 2. Điều kiện cần có để cài đặt
Để ứng dụng phpIPAM chạy được hoàn hảo trên Centos server, cần phải cài đặt các ứng dụng dưới đây
- PHP và các mô-đun PHP bắt buộc
- Apache web server
- Mariadb database
# 3. Các bước cài đặt phpIPAM trên Centos7
- Cài đặt và bật dịch vụ httpd, mariadb
```
yum install -y httpd
yum install -y mariadb-server
```
```
systemctl start httpd
systemctl enable httpd
systemctl start mariadb
systemctl enable mariadb
```
- Cài PHP và module
```
yum install -y php php-cli php-gd php-common php-ldap php-pdo php-pear php-snmp php-xml php-mysql php-mbstring git
```
- Sửa dòng `DocumentRoot "/var/www/html"` thành `DocumentRoot "/var/www/phpipam"`
- Sửa file http.conf tìm đên mục `<Directory "var/www/html"> tức là bắt đầu từ dòng 131 sửa lại như sau
![](https://imgur.com/0QvqyZc.png)

- cd vào thư mục /var/www/ sau đó clone phpipam trên kho bằng câu lệnh
```
cd /var/www
git clone https://github.com/phpipam/phpipam.git
cd phpipam
git checkout 1.3
chown -R apache:apache /var/www/phpipam
chmod -R 777 /var/www/phpipam
cp config.dist.php config.php
```
- Tạo một database với user và password trùng thông tin lưu trong file config.php
```
mysql -u root -p
```
```
create database phpipam;
create user 'phpipam'@'localhost' identified by 'phpipamadmin';
grant all privileges on phpipam.* to 'phpipam'@'localhost';
flush privileges;
```
- Giao diện sau khi cài đặt hiện lên như sau
![](https://imgur.com/PkOH9L8.png)
