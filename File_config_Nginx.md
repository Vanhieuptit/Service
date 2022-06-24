- File cấu hình chính của Nginx là **nginx.conf** nằm tại **/etc/nginx/nginx.conf**
![](https://imgur.com/529TwDo.png)
## Thuật ngữ directice và context trong Nginx
- Trong cấu hình Nginx có hai thuật ngữ quan trọng là **directive** và **context**.
- Mọi thứ nằm trong file **nginx.conf** ngoại trừ các dòng comment bằng đầu bằng dấu `#` thì đều được gọi là directicve. **Directicve** gồm hai loại là:
  - Simple Directive
  - Block Directive
### Simple Directive
- Bao gồm directice name và tham số, ngăn cách nhau bởi dấu cách và kết thúc bằng dấu `;`. Ví dụ:
``` 
events {
    worker_connections  1024;
}
```
- Ở đây `worker_connection 1024` chính là simple directive.
### Block Directive
- Chứa những **simple directive** và đặt tất cả chúng vào trong dấu `{}` và thường được gọi với cái tên là **context**. Ví dụ như **events, http**,... Có 4 context chính trong Nginx:
  - event{} context: dùng để thiết lập cấu hình toàn cục. Và trong mỗi 1 file config, sẽ chỉ có duy nhất một context **even**.
  - http{} context: Dùng để cấu hình Nginx xử lý các request HTTP và HTTPS. Mỗi 1 file config cũng sẽ chỉ có một **http{} context**.
  - server{} context: Nằm trong context **http**, được sử dụng để cấu hình cho từng virtual host. Có thể có nhiều **context server** trong context http. 
- Ví dụ: 
```
events {

}

http {
    server {
        listen 80;
        server_name web1.com;
    }

    server {
        listen 8080;
        server_name web2.com;
    }
}
```
## Cấu hình Nginx để load những trang web tĩnh
- Giả sử, file index.html của trang web tĩnh nằm tại thư mục: **/usr/share/nginx/html/**.
- Cấu hình file **nginx.conf** để load web tĩnh
```
events {
     worker_connections  1024;
}

http {
    server {
        listen 80;
        server_name web1.com;
        root /usr/share/nginx/html/;
    }
}
```
- Dòng cấu hình trên có ý nghĩa khi một request gửi tới, Nginx sẽ tìm kiếm file index.html ở trong thư mục /usr/share/nginx/html/ sau đó trả về cho người dùng
- Nếu trong trang web có những nỗi dung như file css, chúng ta sẽ cấu hình như sau:
```
events {
     worker_connections  1024;
}

http {
    type {
        text/html html;
        text/css css;
    }

    server {
        listen 80;
        server_name web1.com;
        root /usr/share/nginx/html/;
    }
}
```
- Hoặc nếu trang web có nhiều file định dạng khác như là html, css, jpg, jpeg,... Ở trong thư mục /etc/nginx có một file là **minme.types**, sẽ bao gồm các định dạng file khác nhau và thay vì sử dụng block directive **types**, ta sẽ sử dụng simple directive **include** ở trong fiile **nginx.conf** như sau:
```
events {
     worker_connections  1024;
}

http {
    include /etc/nginx/mime.types;

    server {
        listen 80;
        server_name web1.com;
        root /usr/share/nginx/html/;
    }
}
```
- Cách này sẽ giúp ta không phải khai bao từng loại file một.