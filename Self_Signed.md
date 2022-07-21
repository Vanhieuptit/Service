# Tạo chứng chỉ tự ký Self-signed SSL certificate với Nginx 
- Self-sign là cách để chúng ta mã hóa thông tin trao đổi giữa Server và client một cách an toàn. 
- Chứng chỉ này không được bất ký tổ chức phát hành chứng chỉ tin cậy nào có trong trình duyệt web.
- Người dùng không thể sử dụng chứng chỉ để xác thực danh tính của Server một cách tự động.
- Chứng chỉ tự ký có thể phù hợp nếu bạn không có tên miền được liên kết với Server và đối với các trường hợp mà giao diện web được mã hóa không hướng tới user. 
- Nếu bạn có một tên miền thì tốt hơn là nên sử dụng một chứng chỉ CA ký.
# Các bước tiến hành
- Bước 1: Tạo chứng chỉ SSL
- Tạo thư mục chứa private key
```
mkdir /etc/ssl/private
chmod 700 /etc/ssl/private
```
- Tạo self-sign key và certificate
```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt
```
- Trong đó:
  - days365: chỉ ra key có thời hạn 365 ngày
  - keyout: Vị trí lưu key private
  - out: vị trí đặt certificate
- Sau khi thực hiện xong câu lệnh trên thì khai báo tương tự nhưng tạo chứng chỉ tự ký trên apache
- Tạo Diffie-Hellman để việc trao đổi khóa được an toàn giữa server và client.
```
openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```
# Cấu hình Nginx
- Bây giờ các file key và chứng chỉ của bạn trong thư mục /etc/ssl/. Ta cần sửa đổi cấu hình Nginx
- Đầu tiên, tạo đoạn mã cấu hình với thông tin về khóa SSL và vị trí tệp chứng chỉ. 
- Điều chỉnh các server block để xử lý SSL một cách thích hợp.
`vi /etc/nginx/sites-available/test.1lab.xyz.conf`
- Thêm các dòng sau vào khối server block
```
listen 443 http2 ssl;
ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
ssl_dhparam /etc/ssl/certs/dhparam.pem;
```
- Restart lại nginx để kiểm tra kết quả