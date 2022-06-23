- Mô hình lab 1
1[](https://imgur.com/WBtHAbm.png)
Bước 1: Cài Apache
Bước 2: Cài Nginx trên máy ảo thứ 2
Bước 3: Tạo file cấu hình chuyển hướng reverse
```
vi /etc/nginx/conf.d/web1.com.conf
```
- Thêm nội dung sau
```
server {
    listen 80;
    server_name web1.com;
    access_log /var/log/nginx/web1.com.log;
    error_log /var/log/nginx/web1.com.log;
    
    location / {
        proxy_pass http://192.168.50.10:80/;
    }
}
```
- Lúc này, khi truy cập vào domain web1.com Nginx sẽ đóng vai trò là Reverse proxy xử lý và chuyển hướng tất cả các yêu cầu đến Apache.
- Mô hình lab 2
![](https://imgur.com/J6iHyXa.png)
Bước 1: Cài apache cho server 192.168.50.12
```
yum install -y httpd
```
- Bật apache
```
systemctl start httpd
systemctl enable httpd
```
- Cho bật tường lửa cho phép truy cập http
```
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
```
Bước 2: Cài Nginx trên server 192.168.19.10 để làm reverse proxy
- Tải repo epel
```
yum install -y epel-release
```
- Tải Nginx 
``` 
yum install -y Nginx
```
- Bật Nginx
```
systemctl start nginx
systemctl enable nginx
```
- Cấu hình tưởng lửa
```
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
```
Bước 3: Tạo vitual host cho Nginx
- Tạo nơi lưu trữ 2 website
```
mkdir /usr/nginx/web1/
mkdir /usr/nginx/web2/
```
- Cấp quyền đọc được chấp nhận với tất cả các file và thư mục bên trong /usr/nginx/
```
chown -R $USER:$USER /usr/nginx/
chmod -R 755 /usr/nginx/
```
- Tạo file index.html chứa nội dung của trang web
```
mkdir /usr/nginx/web1/index.html
mkdir /usr/nginx/web2/index.html
```
- Thêm nội dung cho file index.html
```
echo "Day la web 1" /usr/nginx/web1/index.html
echo "Day la web 2" /usr/nginx/web2/index.html
```
- Tạo thư mục **sites-available** và **sites-enabled**
```
mkdir /etc/nginx/sites-available/
mkdir /etc/nginx/sites-enabled/
```
- Trong đó: 
    - **sites-available** chứa các cấu hình Virtual host có trên hệ thống.
    - **sites-enabled** chứa các cấu hình Virtual host được kích hoạt để chạy.
- Tạo file config của 2 web
```
mkdir /etc/nginx/sites-available/web1.com.conf
mkdir /etc/nginx/sites-available/web2.com.conf
```
- Thêm nội dung sau vào file web1.com.conf
```
server {
        listen 80;
        server_name web1.com;
        access_log /var/log/nginx/web1.com.log;
        error_log /var/log/nginx/web1.com.log;

        location / {
        proxy_pass http://192.168.50.10:80/;
        }
}
```
- Trong đó dòng proxy_pass http://192.168.50.10:80/ tức là chuyển hướng tới địa chỉ của apache server
- Đối với web2.com.conf 
```
server {
        listen 80;
        server_name web2.com;
        access_log /var/log/nginx/web2.com.log;
        error_log /var/log/nginx/web2.com.log;

        location / {
        proxy_pass http://192.168.50.12:80/;
        }
}
```
- Restart lại dịch vụ
```
systemctl restart nginx
```
- Kết quả 
![](https://imgur.com/edyrdIr.png)
![](https://imgur.com/fCayDLL.png)

