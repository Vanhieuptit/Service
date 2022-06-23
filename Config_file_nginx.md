# Common Gateway Interface (CGI) 
- Là một kỹ thuật cho phép các máy chủ web thực thị một trương trình bên ngoài, thường là để xử lý các yêu cầu của người dùng.
- Các chương trình như vậy thường được gọi là CGI scripts 
# FastCGI 
- Là một giao thức nhị phân để giao tiếp các chương trình tương tác với máy chủ web
- Là phiên bản nâng cấp hơn của CGI nhằm cho phép máy chủ xử lý nhiều yêu cầu trang web hơn trên một đơn vị thời gian (tức là xử lý cùng một lúc)
# Tổng quan file cấu hình
- Về tổng quan, file config chính sẽ được sắp xếp theo cấu trúc cây, xác định bởi tập hợp hay dấu `{}`. 
- Các thành phần được xác định bởi dấu đó được gọi là "context"
- Một tệp cấu hình mặc định khi ta cài nginx sẽ nằm ở `/etc/nginx/nginx.conf`
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
        error_page 404 /404.html;
        location = /404.html {
        }
        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }
}
```
- Tệp được cấu trúc thành Contexts. Đầu tiên là **events context**, tiếp theo là **httpd context**. Với cấu trúc như trên cho phép một số lớp nâng cao cấu hình. Bởi vì trong mỗi context có thể có các context lồng nhau khác có thể kế thừa và ghi đè khi cần thiết.
- Nhiều thứ trong tệp này có thể được điều chỉnh dựa trên nhu cầu của bạn. Tuy nhiên với sự đơn giản của Nginx cho nên bạn có thể hoàn toàn sử dụng cấu hình mặc định mà không cần chỉnh sửa gì. Ta cùng đi tìm hiểu một số phần quan trọng trong tệp cấu hình này
  - **worker_processes**: Xác định số lượng worker process mà Nginx sẽ sử dụng. Do Nginx là single thread cho nên con số này thường bằng với số core của CPU.
  - **worker_connections**: đây là số lượng kết nối tối đa tại một thời điểm cho mỗi worker process và cho biết worker process có bao nhiêu người dùng có thể được phục vụ đồng thời bởi Nginx. Con số này càng lớn thì số lượng người dùng được Nginx phục vụ sẽ càng cao.
  - **access_log** và **error_log**: đây là các tệp mà Nginx sử dụng để ghi lại các lần lỗi và truy cập
-  
### Khối http
- Bao gồm các chỉ thị để xử lý lưu lượng truy cập web, thường được gọi là **universal**. Đó là bởi vì chúng được chuyển đến từng cấu hình trang web do NGINX cung cấp.
### Khối server
- Khối được hiển thị trên có chỉ thị **include**. Điều này thông báo cho NGINX nơi có thể tìm thấy các tệp cấu hình trang web.
  - Khi cài đặt từ kho lưu trữ chính thức của NGINX, dòng sẽ đọc gồm /etc/nginx/conf.d/*.conf. Mỗi trang web được lưu trữ bằng NGINX phải có một tệp cấu hình duy nhất trong /etc/nginx/conf.d/ và tên sẽ được định dạng là example.com.conf. Những trang web đã bị vô hiệu hoá-không được NGINX cung cấp-phải có tên example.com.conf.disabled.
  -  Theo nguồn cài đặt, ta có thể tìm thấy tệp cấu hình minh hoạ tại /etc/nginx/conf.d/default.conf
### Listen port là gì?
- Chỉ thị lắng nghe thông báo cho NGINX về tên máy/IP và cổng TCP, do đó nó nhận ra nơi nó phải lắng nghe các kết nối HTTP.
- Đối số **default_server** có nghĩa là máy chủ ảo này sẽ trả lời các yêu cầu trên cổng 80 không khớp với câu lệnh listen của máy chủ ảo riêng biệt.
### Server_name
- Cho phép một số tên miền được phân phát chỉ từ một địa chỉ IP và máy chủ sẽ xác định miền nào đó sẽ phân phát theo tiêu đề yêu cầu nhận được.
- Nói chung, ta nên tạo một tệp cho mỗi trang web hoặc miền mà ta muốn lưu trữ trên máy chủ của mình. Ví dụ:
  - Xử lý các yêu cầu cho example.com và www.example.com:
  - Lệnh server_name có thể sử dụng các ký tự đại diện.*.example.com và .example.com yêu cầu máy chủ xử lý các yêu cầu cho tất cả các miền phụ example.com:
```
server_name *.example.com;
server_name .example.com; 
```
### Khối location
- Cài đặt vị trí của NGINX giúp bạn thiết lập cách thức mà NGINX phản hồi các yêu cầu về tài nguyên bên trong máy chủ. Vì lệnh **server_name** thông báo cho NGINX cách nó xử lý các yêu cầu cho miền, các lệnh vị trí sẽ áp dụng cho các yêu cầu đối với một số thư mục và tệp nhất định
#### Root và chỉ mục
- Khi NGINX đã xác định chỉ thị vị trí phù hợp nhất cho một yêu cầu cụ thể, phản hồi của nó sẽ dựa trên nội dung của khối chỉ thị vị trí được liên kết.Ví dụ
```
location / {

root html;

index index.html index.htm;

}
```
- Trong ví dụ này, chúng ta có thể thấy rằng tài liệu gốc nằm trong thư mục html/
- Dưới cài đặt mặc định NGINX, đường dẫn đầy đủ của vị trí là /etc/nginx/html/
**Yêu cầu**: http://example.com/blog/includes/style.css
**Trả về**: NGINX sẽ cố gắng cung cấp tệp được tìm thấy tại /etc/nginx/html/blog/includes/style.css.
- Lưu ý:  Các đường dẫn tuyệt đối cho chỉ thị gốc có thể được sử dụng nếu bạn muốn. Biến chỉ mục thông báo cho NGINX tệp nào nó sẽ phân phát khi hoặc nếu không có biến nào được chỉ định.
- Vì vậy, ví dụ:
**Yêu cầu**: http://example.com
**Trả về**: NGINX sẽ cố gắng cung cấp tệp được tìm thấy tại /etc/nginx/html/index.html.
Khi một số tệp được chỉ định cho lệnh chỉ mục, danh sách sẽ được xử lý theo thứ tự và NGINX sẽ thực hiện yêu cầu với tệp đầu tiên được tìm thấy là tồn taị. Nếu không thể tìm thấy index.html trong thư mục có liên quan, thì index.html sẽ được sử dụng để thay thế. Thông báo 404 sẽ được gửi trong trường hợp cả hai đều không tồn tại.
- Một ví dụ khác nâng cao hơn
```
location / {

root /srv/www/example.com/public_html;

index index.html index.htm;

}

location ~ \.pl$ {

gzip off;

include /etc/nginx/fastcgi_params;

fastcgi_pass unix:/var/run/fcgiwrap.socket;

fastcgi_index index.pl;

fastcgi_param SCRIPT_FILENAME /srv/www/example.com/public_html$fastcgi_script_name;

}
```
- Ở đây, chúng ta có thể thấy rằng khối vị trí thứ hai xử lý tất cả các yêu cầu đối với tài nguyên kết thúc bằng phần mở rộng `.pl` và nó chỉ định một trình xử lý fastcgi cho chúng. NGINX sẽ sử dụng chỉ thị vị trí đầu tiên nếu không tài nguyên được tìm thấy trên hệ thống tệp tại /srv/www/example.com/public_html/. Khi không có tên tệp chính xác nào được xác định trong yêu cầu, NGINX sẽ tìm kiếm tệp index.html hoặc index.htm và cung cấp tệp đó.
- Thông báo lỗi 404 sẽ được trả về nếu không có tệp chỉ mục nào được tìm thấy.
- Cụ thể: Hãy xem những yêu cầu mẫu
- Yêu cầu: http://example.com/
- Trả về: /srv/www/example.com/public_html/index.html nếu nó tồn tại. Nếu không, nó sẽ phân phát /srv/www/example.com/public_html/index.htm. Và nếu cả hai điều này đề không tồn tại, lỗi 404 sẽ được cung cấp.
- Yêu cầu: http://example.com/blog/
- Trả về: /srv/www/example.com/public_html/blog/index.html nếu điều này tồn tại. Nếu không thể tìm thấy tệp vì tệp không tồn tại, một /srv/www/example.com/public_html/blog/index.htm sẽ được cùng cấp. Nếu không tồn tại, NGINX sẽ trả về lỗi 404.
## Giải thích file cấu hình 
```
user nginx;
# Nginx gồm một tiến trình trình (process) chính và các tiến trình con (worker_processes)
# Tiến trình chính để nạp cấu hình và quản lý các tiến trình con, các tiến trình con
# thì nhận các yêu cầu truy vấn gửi đến và xử lý nó. Mỗi tiến trình con chạy trên một nhân CPU
# nên tốt nhất thiết lập số tiến trình con bằng số nhân CPU bạn có, lấy bằng lệnh
# cat /proc/cpuinfo | grep processor | wc -l
worker_processes 8;

# error_log /var/log/nginx/error.log crit;
error_log off;
pid /run/nginx.pid;

# worker_rlimit_nofile = worker_connections*2
worker_rlimit_nofile 10000;


# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

# Thiết lập các worker xử lý thế nào khi có các sự kiện kết nối
events {
    # Số client phục vụ đồng thời bởi nginx.
    # Kết hợp với Worker processes, số lớn nhất trên mỗi giây tính theo
    # Worker processes * Worker connections
    # Thiết lập bởi số process đồng thời cho phép mỗi core, dùng lệnh ulimit -n để biết
    worker_connections 1024;

    use epoll;

    # On chấp nhận các kết nối mới một lúc, Off từng kết nối một được chấp nhận
    # On chấp nhận kết nối mới nhiều nhất có thể, cận thận bị ngập kết nối
    multi_accept off;
}

http {
    # log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                   '$status $body_bytes_sent "$http_referer" '
    #                   '"$http_user_agent" "$http_x_forwarded_for"';
    # access_log  /var/log/nginx/access.log  main;
    # Tắt lưu log giảm tải truy cập vào đĩa
    access_log off;


    # https://nginx.org/en/docs/http/ngx_http_core_module.html#open_file_cache
    open_file_cache max=200000 inactive=5m;
    open_file_cache_valid 60s;
    open_file_cache_min_uses 5;
    open_file_cache_errors on;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   30;
    types_hash_max_size 2048;

    # allow the server to close connection on non responding client, this will free up memory
    reset_timedout_connection on;

    # Off header version nginx
    server_tokens off;

    gzip on;
    gzip_min_length 10240;
    gzip_comp_level 1;
    gzip_vary on;
    gzip_disable msie6;
    gzip_proxied expired no-cache no-store private auth;
    gzip_types
    # text/html is always compressed by HttpGzipModule
    text/css
    text/javascript
    text/xml
    text/plain
    text/x-component
    application/javascript
    application/x-javascript
    application/json
    application/xml
    application/rss+xml
    application/atom+xml
    font/truetype
    font/opentype
    application/vnd.ms-fontobject
    image/svg+xml;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    # các khối server
    server {
       # ...
    }
    server {
       # ...
    }
}
```
# Xử lý client request trong webserver
## Nginx Block Configurations
- Nginx phân chia config trong nhiều block khác nhau tạo thành cấu trúc hình cây. 
- Mỗi lần có request được gửi lên server, nginx sẽ xác định config block nào sẽ được sử dụng để handle request
- Một khối block để handle request có dạng như sau
```
server {
    location {
    
    }
}
```
- Một server Block là một tập hợp của nhiều cấu hình nginx quyết định hướng xử lý cho các request khác nhau dựa trên domain name port và IP address.
- Một location block nằm trong server block được sử dụng để định nghĩa mà nginx có thể handle request cho nhiều request với các dạng URL khác nhau.
- URL có thể được chia thành bất cứ cách nào mà admin hệ thống muốn để phân loại request.
## Cách nginx quyết định server block nào sẽ handle request
- Nginx cho phép khai báo server trong config thành những khối block riêng biệt cho từng virtual host thì ta cần phải xác định được khi nào thì request nào sẽ được xử lý ở đâu. NGINX thực hiện việc này thông qua 2 thuộc tính trong config đó là listen directice và server_name directive.
### Quá trình Nginx tìm ra server web nào mà request sẽ được map đến.
- Đầu tiên, Nginx sẽ nhìn vào địa chỉ IP và port của request, Nginx sẽ đối chiếu với mỗi listen directive của mỗi server block để tìm ra block nào sẽ giải quyết request này.
- Thông thường **listen directive** sẽ được định nghĩa là **IP** hay **port** mà server sẽ trả lời response
- Nếu không khai báo thì nginx sẽ nghe default (0.0.0.0:80) hoặc (0.0.0.0:8080). Tóm lại là reponse sau khi xử lí xong sẽ trả về response về lại cổng 80.
- Listen directive có thể được chia thành
  - Địa chỉ Ip đơn (default sẽ chạy ở cổng 80), hoặc combo bao gồm ip và port.
  - path to unix socket.
-  Trong quá trình xác định xem request sẽ được chuyển đến server block nào, nginx sẽ cố xác định thông tin từ listen directive trước theo quy tắc như sau:
  - NGINX sẽ gán giá trị mặc định cho block bị thiếu thông tin. VD: block không có listen directive sẽ sử dụng value là `0.0.0.0:80`, block chỉ set địa chỉ sẽ mặc định nghe ở cổng 80, block chỉ có mỗi port sẽ chuyển về nghe ở địa chỉ 0.0.0.0.
- Sau quá trình find theo ip và port, nginx sẽ find tiếp theo server_name directive và đối chiếu nó với filed Host trong request.
```
server {
    listen 80;
    server_name *.example.com;

    . . .

}
```
- Tóm tắt lại chức năng của listen directive là chỉ ra cổng mà nginx lắng nghe cho giao thức truyền tới. Ví dụ listen 80 nghĩa là tất cả những request gửi tới bằng giao thức http sẽ được xử lí tại đây. còn server_name directive sẽ đối chiếu với field Host trong header của request để xem request có được xử lí theo khối block server này không
## Location directive block
- Sau khi đã chọn được server block nào sẽ tiếp nhận request này thì nginx sẽ tiếp tục phân tích URL của request để tìm ra hương xử lý của request dựa vào cac block location có syntax như sau
