# Cài PHP
- PHP là thành phần để hiển thị nội dung động, nó xử lý các tập lệnh, kết nối với cơ sở dữ liệu Mysql để lấy thông tin và chuyển nội dung đã xử lý cho máy chủ web để hiển thị.
- Không giống với Apachej, Nginx cần một chương trình trung gian giữa nó và trình biên dịch PHP. Nó được gọi là **PHP-FPM**
- Ngoài ra ta còn cần thêm php-mysql, một module PHP cho phép PHP giao tiếp với cơ sở dữ liệu dựa trên Mysql.
## Cài đặt
- Ta tiến hành cài đặt PHP như bình thường đã cài ở LAMP
- Ở phần cài options cho PHP, ta cài thêm `php-fpm` bằng câu lệnh
```
yum install php php-fpm -y
```
### Sau khi đã cài xong, ta sẽ tiến hành tạo user và phân quyền
- Lý thuyết :
  - FPM là viết tắt của "FastCGI Process Manager", mỗi khi có 1 request được gửi đến, nó sẽ được xử lý bởi worker(process), PHP-FPM có nhiệm vụ điều khiển công việc tải request đến worker, sinh và diệt các worker.
  - Tập hợp của các worker lại với nhau được gọi là pool(nhóm), và với 1 server PHP-FPM có thể có nhiều pool, trong mỗi pool sẽ lại có nhiều worker đang xử lý request.
- Cấu hình:
- Tạo user cho từng website, không cung cấp cho chúng quyền đăng nhập hoặc liên kết thông tin nào khác:
```
useradd -s /sbin/nologin website1
useradd -s /sbin/nologin website2
```
- Thêm chúng vào group nginx để cho phép webserver tương tác với user và ngược lại
```
usermod -a -G website1 nginx
usermod - a -G website2 nginx
```
- Tiếp theo, tạo thư mục cho từng web riêng biệt và gán quyền cho user tương ứng vừa tạo:
```
mkdir /home/website1/public_html
chown -R website1. /home/website1/public_html
chmod 750 /home/website1/
mkdir /home/website2/public_html
chown -R website2. /home/website2/public_html
chmod 750 /home/website2/
```
- việc cài đặt phân quyền này sẽ làm user website1 không thể xem hay can thiệp được vào dữ liệu website2
- Tạo php-fpm pool mới cho từng website bằng cách copy file cấu hình mặc định:
```
cp /etc/php-fpm.d/www.conf /etc/php-fpm.d/fpm-website1.conf
cp /etc/php-fpm.d/www.conf /etc/php-fpm.d/fpm-website2.conf
```
- Xoá bỏ pool mặc định
```
rm -f /etc/php-fpm.d/www.conf
```
- Cấu hình pool website1
```
vi /etc/php-fpm.d/fpm-website1.conf
```
- Ý nghĩa của việc chỉnh sửa trên là 
  - Cấp quyền truy cập vào các thư mục như httpd cho user **website1**.
  - Thay đổi php-fpm từ liten trên cổng 9000 qua TCP sang listen trực tiếp trên socket file /var/run/website1-fpm.sock.
  - Thay đổi owner và group của tệp socket trên thành nginx.
### Cấu hình Nginx
- Ngoài file cấu hình mặc định /etc/nginx/nginx.conf, Nginx còn cung cấp thư mục /etc/nginx/conf.d để lưu trữ các file cấu hình cho từng trang web riêng biệt (khá tương tự với virtual host của Apache)
- Chúng ta sẽ tạo mới file cấu hình cho website1 và website2 của mình tại thư mục này
```
vi /etc/nginx/conf.d/website1.conf
```
- Ý nghĩa của file cấu hình
```
listen - cổng mà site sẽ lắng nghe. website1 listen trên port 80 nên sẽ ghi đè lên cấu hình mặc định của nginx.
server_name – Tên domain/sub của site
root – đường dẫn mã nguồn
fastcgi_pass – đường dẫn của file sock php-fpm
```
- Kiểm tra hoạt động của PHP
- Tạo 1 file `info.php` tương tự như trên LAMP. 
```
echo "<?php phpinfo(); ?>" > /home/website1/public_html/info.php
```
