# NGINX là gì?
- NGINX là một phần mềm webserver tương tự Apache server. 
- NGINX hiệu quả hơn Apache, nó có thể làm được những thứ như load balancing, HTTP caching, hay sử dụng như một reverse proxy. 
# Cài đặt Nginx
Bước 1:Cập nhật hệ thống
```
yum check-update || yum update -y
```
- Cài đặt các gói cần thiết bằng lệnh
```
yum install yum-utils -y
```
- Thiết lập nginx repository để có thể cài đặt được Nginx thông qua `yum` bằng cách sử file
```
vi /etc/yum.repos.d/nginx.repo
```
- Dán nội dung dưới đây vào
```
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
```
Bước 2: Cài đặt Nginx
```
yum install nginx -y
```
Bước 3: Khởi động nginx
```
systemctl enable nginx
systemctl start nginx
```
Bước 4: Cấu hình firewall cho phép truy cập
```
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
```
## File cấu hình Nginx
- Tất cả các file cấu hình Nginx đều nằm trong thư mục /etc/nginx/.
- Tệp cấu hình chính của Nginx là /etc/nginx/nginx.conf.
- Để duy trì cấu hình Nginx dễ dàng hơn, bạn nên tạo một tệp cấu hình riêng cho từng miền.
- Các file cấu hình phải kết thúc bằng .conf và được lưu trữ trong thư mục /etc/nginx/conf.d.
- Nếu tên miễn của bạn là mydomain.com thì tệp cầu hình của bạn phải được đặt tên /etc/nginx/conf.d/mydomain.com.conf
- Các file log của Nginx (access.log và error.log) nằm trong thư mục /var/log/nginx/.
## Cấu hình LEMP
- Tương tự như cấu hình LAMP nhưng ta thay Apcahe thành Nginx.
Bước 1: Sửa file `/etc/php-fpm.d/www.conf` và chỉnh sửa nó 
  - Tìm kiếm các dòng lệnh sau và thay `apache` thành `nginx`
```
user = apache
group = apache
```
Bước 2: Xác định vị trí lệnh `listen`, theo mặc định `php-fpm` sẽ lắng nghe trên một máy chủ và cổng cụ thể qua TCP. Thay đổi cài đặt để nó lắng nghe trên socket file, vì điều này giúp cải thiện hiệu suất tổng thể của máy chủ.
- Tìm đến dòng có chứa câu lệnh `listen = 127.0.0.1:9000` và sửa thành
`listen = /var/run/php-fpm/php-fpm.sock;`
- Thay đổi cài đặt của chủ sở hữu và nhóm cho tệp. Xác định vị trí lệnh `listen.owner`, `listen.grouup`, `listen.mode`. Loại bỏ dấu `;` dấu trước ở đầu dòng. Sau đó thay đổi thành nginx.
```
;listen.owner = nobody  => listen.owner = nginx
;listen.group = nobody  => listen.group = nginx
;listen.mode = 0660     => listen.mode = 0660
```
- Kích hoạt và khởi động lại `php-fpm`
`systemctl start php-fpm`