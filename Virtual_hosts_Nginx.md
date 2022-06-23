# Cài đặt Nginx
- Cài kho mở rộng
```
# yum install -y epel-release
# yum install -y nginx
```
- Bật dịch vụ
```
# systemctl start nginx
# systemctl enable nginx
```
- Bật firewall
```
# firewall-cmd --add-service=http --permanent
# firewall-cmd --add-service=https --permanent
# firewall-cmd --reload
```
# Cấu hình Nginx
## File cấu hình
- Để kiểm tra cấu hình thực hiện lệnh
```
nginx -t
```
- Nó cho biết cấu hình có bị lỗi không, cũng cho biết đường dẫn file cấu hình là tại `/etc/nginx/nginx.conf`
- Ngoài ra trong nginx.conf thường cũng có chỉ thị trong khối http là 
```
include /etc/nginx/conf.d/*.conf;
```
- Nên khi nạp cấu hình trong file /etc/nginx/nginx.conf nó cũng nạp các file `.conf` trong thư mục /etc/nginx/conf.d. Vì vậy để viết cấu hình tạo virtual host cho nginx ta có thể viết trong `/etc/nginx/nginx.conf` hoặc file `.conf` bất kỳ bạn tạo ra trong `/etc/nginx/conf.d/`.
- Để tạo virtual host, chỉ việc tạo ra các khối server trong khối http thích hợp.
## Tạo thư mục chứa trang web
```
mkdir -p /usr/share/web1.com/html
mkdir -p /usr/share/web2.com/html
```
## Cấp quyền cho thư mục 
```
chown -R $USER:$USER /usr/share/web1.com/html/
chown -R $USER:$USER /usr/share/web2.com/html/
```
- Cấp quyền cho phép đọc vào thư mục web chung và tất cả các tệp và thư mục bên trong để các trang có thể phục vụ chính xác.
```
chmod -R 755 /usr/share/
```
## Tạo trang demo cho mỗi trang web
```
echo "Day la web 1" > /usr/share/web1.com/html/index.html
echo "Day la web 2" > /usr/share/web2.com/html/index.html
```
## Tạo file khối server mới
- Các khối server là những gì chỉ định cấu hình của các trang web riêng biệt và chỉ ra cách để máy nginx sẽ đáp ứng các yêu cầu tên miền khác nhau.
- Tạo thư mục `sites-available` chứa tệp cấu hình và thư mục `sites-enabled` 
```
mkdir /etc/nginx/sites-available
mkdir /etc/nginx/sites-enabled
```
## Sửa file cấu hình để khi truy cập, nginx sẽ đọc các file tìm đúng khối server 
``` 
vi /etc/nginx/nginx.conf
```
- Thêm các dòng sau vào khối **http{}**
```
include /etc/nginx/sites-enabled/*.conf;
server_name_hash_bucket_size 64;
```
- Dòng đầu tiên là hướng dẫn Nginx tìm kiếm các khối server trong thư mục `sites-enabled`. 
- Dòng thứ 2 là tăng dung lượng bộ nhớ được phân bổ để phân tích tên miền
## Tạo tên khối máy chủ đầu tiên
- Mặc định, Nginx chứa một khối máy chủ được gọi là `default.conf` mà có thể sử dụng làm mẫu cho các cấu hình riêng của mình. Vì vậy ta có thể sao chép tệp này ra để chỉnh sửa thành cấu hình riêng của mình
```
cp /etc/nginx/conf.d/defaut.conf /etc/nginx/sites-available/web1.com.conf
```
- Phân tích tệp cấu hình
```
server {
    listen       80;
    server_name  web1.com www.web1.com;
    location / {
        root   /usr/share/web1/html;
        index  index.html index.htm;
    }
    try_files $uri $uri/ =404;
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```
- Server name: cho Nginx biết yêu cầu trỏ đến khối máy chủ này. 
- Root: trỏ tới tài liệu của trang web mà bạn đã tạo
- try_files: kết thúc bằng lỗi 404 nếu không tìm thấy tên tệp hoặc thư mục mong muốn.
- Tương tự ta cũng tạo tệp khối máy chủ thứ 2 và sửa chúng
```
server {
    listen       80;
    server_name  web2.com www.web2.com;
    location / {
        root   /usr/share/web2/html;
        index  index.html index.htm;
    }
    try_files $uri $uri/ =404;
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```
## Tạo một liên kết tượng trưng đến thư mục sites-enabled
```
ln -s /etc/nginx/sites-available/web1.com.conf /etc/nginx/sites-enabled/web1.com.conf
ln -s /etc/nginx/sites-available/web2.com.conf /etc/nginx/sites-enabled/web2.com.conf
```
- Khởi động lại dịch vụ `systemclt restart nginx`