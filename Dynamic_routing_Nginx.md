# Prefix match
- Ví dụ, ta có một file cấu hình **nginx.conf** như sau:
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
    location /user {
        return 200 "Hello User!\n";
        }
    }
}
```
- Lưu ý, block directive location luôn nằm trong block directive server, có thể có nhiều location ở trong server.
- Bây h nếu một người gửi request đến `web1.com/user` kết quả sẽ trả về dòng chữ **Hello User!**
- Và nếu gửi một request tới `web1.com/user1` kết quả trả về vẫn sẽ là **Hello User!**. 
- Kết quả giống nhau bởi vì khi mình sử dụng `location /user`, Nginx sẽ match tất cả URI bắt đầu bằng /user và trả về cùng 1 response là: Hello User!. Đây được gọi là prefix match.
# Exact match
- Tương tự file cấu hình kia, ta sử lại dong loacation thành
```
 location = /user {
        return 200 "Hello User!\n";
```
- Lúc này chỉ khi gửi đúng một request tới web1.com/user thì kết quả mới trả về **Hello User!**. Còn nếu khác đi web1.com/user1 kết quả sẽ trả về status code là 404. Đây được gọi là exact match
# Regex match
- Ta lại sửa file cấu hình thành
```
 location ~ /user[0-9] {
        return 200 "Hello User!\n";
```
- Lúc này Nginx sẽ match với những URI thoả mãn biểu thức chính quy user1 hoặc user2 hoặc ... đến user9. Còn lại các biểu thức khác sẽ trả về 404 
- Trong Nginx thì nó sẽ ưu tiên **regex match** trước, rồi mới đến **prefix match**. Có nghĩa là, nếu URI thoả mãn **regex match** và **prefix match** thì sẽ trả về reponse của **regex match**. Cụ thể, ví dụ sau:
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
    location /user1 {
        return 200 "This is prefix match.\n";

    }
    location ~ /user[0-9] {
        return 200 "This is regex match.\n";
        }
    }
}
```
- Mặc dù đã cấu hình location /user1 là **This is prefix match** , tuy nhiên nếu ta gửi request tới web1.com/user1, kết quả sẽ ưu tiên trả về **This is regex match.**
- Tuy nhiên Nginx cũng cho phép chúng ta thay đổi thứ tự ưu tiên nay, bằng cách sử dụng **^~** trong **prefix match**, cụ thể như sau:
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
    location ^~ /user1 {
        return 200 "This is prefix match.\n";

    }
    location ~ /user[0-9] {
        return 200 "This is regex match.\n";
        }
    }
}
```
- Lúc này Nginx sẽ ưu tiên trả về **This is prefix match.**
# Biến trong Nginx
- Biến trong Nginx cũng giống như trong các ngôn ngữ lập trình khác. Chúng ta có thể dùng directive set để khai báo biến và gán giá trị cho nó, với cú pháp như sau:
```
set $<variable_name> <variable_value>
```
- Biến trong Nginx có 3 kiểu là:
  - String
  - Interger
  - Boolean
- Ví dụ ta cấu hình file **nginx.conf** như sau:
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

        return 200 "Hostname: $host\nURI: $uri\nQuery prams: $args\n";
    }
}
```
- Khi gửi một request tới web1.com/user/user?name=TEST, kết quả sẽ trả về là
```
Hostname: web1.com
URI: /user
Query prams: name=TEST
```
- Các biến **$host**, **uri**, **args** là những biến có sẵn NGINX cung cấp.
  - $host: Lưu trữ hostname của server
  - $uri: Lưu trữ URI
  - $args: Lưu trữ query prams.
# Redirect và Rewrite
- Có file **nginx.conf** như sau:
```
events {

}


http {
    include /etc/nginx/mime.types;

    server {
	listen 80;

	server_name web1.com;

	root /usr/share/html;

	location = /index_page {
	   return 307 /index.html;
	}
	
	location = /about_page {
	   return 307 /about.html;
	}
    }
}
``` 
- Gửi request tới **web1.com/index_page**, Nginx sẽ redirect ta tới **web1.com/index.html**
- Nginx trả về code 307, và điều hướng tới **web1.com/index.html**. Khi sử dụng **redirect**, Nginx sẽ thay đổi URL trên browser. Ngoài **redirect**, còn có một cách khác là sử dụng **rewrite**.
- Với **rewrite**, mình sẽ sửa file **nginx.conf** như sau:
```
events {

}

http {
    include /etc/nginx/mime.types;

    server {
	listen 80;

	server_name web1.com;

	root /usr/share/html;
    
    rewire /index_page /index.html:

    rewite /about_page /about.html;
    }
}
```
- Khi gửi request tới **web1.com/index_page**, kết quả sẽ trả về status code là 200 với nội dung của page **index.html** nhưng URL lại không bị thay đổi.
- Sự khác nhau giữa sử dụng **redirect** và **rewrite** là khi sử dụng **redirect**, Nginx sẽ thay đổi URL trên browser, còn đối với **rewrite**, Nginx sẽ ngầm điều hướng tới page mà mình config và giữ nguyên URL.