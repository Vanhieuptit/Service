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
yum install -y epel-release
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
- Tệp cấu hình chính của Nginx là
```
/etc/nginx/nginx.conf.
```
- File cấu hình ban đầu sau khi cài đặt nginx sẽ như sau
- Để dễ dàng phân tích, tôi sẽ loại bỏ những dòng comment bắt đầu bằng dấu `#`
```
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;
include /usr/share/nginx/modules/*.conf;
events {
    worker_connections 1024;
}
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 4096;
    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;
    include /etc/nginx/conf.d/*.conf;
    server {
        listen       80;
        listen       [::]:80;
        server_name  _;
        root         /usr/share/nginx/html;
        include /etc/nginx/default.d/*.conf;
        error_page 404 /404.html;
        location = /404.html {
        }
        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }
}

```
- `user`: định nghĩa cho biết người dùng hệ thống linux nào sẽ có quyền chạy các máy chủ Nginx.
- `worker_process`: xác định có bao nhiêu cores của CPU làm việc với Nginx. Nginx sẽ sử dụng một CPU để xử lý các tác vụ của mình. Tuỳ theo mức độ hoạt động của web server mà chúng ta có thể thay đổi lại thiết lập này. Ví dụ với các web server hay sử dụng về ssl, gzip thì ta nên đặt chỉ số của **worker_processes** này lên cao hơn. Nếu website có số lượng các tệp tin tĩnh nhiều, và dung lượng của chúng lớn hơn bộ nhớ RAM thì việc tăng **worker_processes** sẽ tối ưu băng thông đĩa của hệ thống. Để xác định số cores của CPU của hệ thống ta có thể thực hiện lệnh.
```
cat /proc/cpuinfo | grep processor
```
- `pid`: xác định nơi Nginx sẽ ghi lại master process ID, hoặc PID. PID được sử dụng bởi hệ điều hành để theo dõi và gửi tín hiệu đến quá trình Nginx.
- `worker_connections`: sẽ cho biết số lượng connection mà CPU sẽ xử lý. Mặc định, số lượng connection này được thiết lập là 1024. Để xem về mức độ giới hạn sử dụng của hệ thống bạn có thể dùng lệnh
```
ulimit -n
```
### http 
- Đây là phần cấu hình để Ngixn xử lý lưu lượng web HTTP.
- `tcp_nopush`: 
  - Được sử dụng ở trong khối `server`, `http`, `location`
  - Cho phép hoặc vô hiệu hoá tuỳ chọn socket TCP_NOPUSH (FreeBSD) hoặc TCP_CORK (Linux). Chỉ thị này chỉ áp dụng khi chỉ thị `sendfile` được cho phép. Nếu chỉ thị `tcp_nopush` được thiết lập là **on**, Nginx sẽ cố gắng truyền toàn bộ phần header phản hồi HTTP trong 1 gói tin TCP.
- `tcp_nodelay`: 
  - Sử dụng trong khối: `server`, `http`, `location`
  - Cho phép hoặc vô hiệu hoá tuỳ chọn socket TCP_NODELAY cho chỉ các kết nối **keep-alive**.
  - Giá trị mặc định là **on**.
- `keepalive_timeout`:
  - Sử dụng trong khối: `server`, `http`, `location`
  - Định nghĩa số giây mà máy chủ sẽ chờ trước khi đóng 1 kết nối **keep-alive**. 
  - Giá trị ở đây đang để là 65.
  - `types_hash_max_size`: Định nghĩa kích thước tối đa của 1 entry trong bảng băm các loại MIME.
- `include /etc/nginx/mime.types`:
  - Chỉ thị này có vai trò trong việc thêm nội dung từ một file khác vào trong cấu hình Nginx. Điều này có nghĩa là bất cứ điều gì được viết trong tập tin **mime.types** sẽ được hiểu là nó được viết bên trong khối **http{}**.
  - Điều này cho phép bạn bao gồm một số lượng dài của các chỉ thị trong khối **http{}** mà không gây lộn xộn lên các tập tin cấu hình chính. Và nó giúp tránh quá  nhiều dòng mã cho mục đích dễ đọc.
- `include /etc/nginx/conf.d/*.conf;` : sẽ bao gồm các tệp tin có đuổi .conf
- `default_type`: định nghĩa loại mime mặc định. Khi Nginx phục vụ 1 tập tin, phần mở rộng của tập tin này được đối chiếu với các loại đã biết được khai báo bên trong các khối types để trả về loại mime chính xác như giá trị của trường Content-type trong tiêu đề http. Nếu phần mở rộng không khớp với bất kỳ loại nào, thì giá trị của chỉ thị **default_type** được dùng.
# Tài liệu tham khảo
https://viblo.asia/p/tim-hieu-va-huong-dan-setup-web-server-nginx-OREGwBwlvlN
https://mangmaytinh.net/threads/cau-hinh-nginx.49/
