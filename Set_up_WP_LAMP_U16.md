Bước 1: Cài đặt Apache
```
apt update -y
apt install apache2 -y
```
- Khởi động dịch vụ apache2 và cho phép dịch vụ tự khởi động lại khi bạn khởi động lại máy
```
systemctl start apache2.service
systemctl enable apache2.service
```
Bước 2: Cài đặt MariaDB
- Bạn có thể sử dụng MySQL hoặc MariaDB. Trong bài này sẽ sử dụng MariaDB
```
apt install mariadb-server mariadb-client -y
```
Bước 3: Cài đặt PHP và các extention sử dụng cho WordPress
```
apt install -y php libapache2-mod-php php-cli php-fpm php-json php-pdo php-mysql php-zip php-gd php-mbstring php-curl php-xml php-pear php-bcmath
```
Bước 4: Tạo database cho WordPress (tương tự Centos7)
Bước 5: Download mã nguồn Wordpress
```
apt-get install -y wget unzip
cd /var/www/html
wget https://wordpress.org/latest.zip
```
- Giải nén
```
unzip latest.zip
mv wordpress/* .
rm -f index.html
```

Bước 6: Cấu hình Wordpress
- Tạo file cấu hình
```
cd /var/www/html
cp wp-config-sample.php wp-config.php
```
- Khai báo thông tin cơ sở dữ liệu vừa tạo ở bước 4
```
systemctl restart apache2.service
```
Bước 7. Thiết lập cấu hình ban đầu cho trang web (như centos7)

