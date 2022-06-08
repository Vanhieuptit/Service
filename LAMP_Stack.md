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
```sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config && sentenfo
```
### Bước 2: Cài đặt Apache
- Chạy các lệnh
```
yum -y install epel-release
yum -y update
yum -y install httpd
```
- Mở port cho phép truy cập website
```
firewall-cmd --add-service=http --permanent
firewall-cmd --add-service=https --permanent
firewall-cmd --reload
```
### Cài đặt MariaDB
- Ở đây, ta sẽ cài đặt MariaDB10. Ta cũng có thể cài phiên bản mới nhất bằng cách truy cấp http://yum.mariadb.org để kiểm tra các phiên bản.
Bước 1: Tạo file repo
- Theo mặc định repo của CentOS chỉ có sẵn MariaDB 5. Để cài đặt MariaDB 10 các bạn cần tạo repo riêng
- Câu lệnh `vi /etc/yum.repos.d/mariadb.repo` và nhập nội dung như sau:
```
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.5.2/rhel7-amd64/
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```
Bước 2: Cài đặt MariaDB bằng lệnh sau
```
yum install MariaDB-server MariaDB-client -y
```
`yum install mariadb-server mariadb`
Bước 3: Đặt mật khẩu root bằng các câu lệnh
```
systemctl enable mariadb
systemctl start mariadb
mysql_secure_install
```
Sau đó thực hiện như sau
![](https://imgur.com/uJwdyVw.png)




