- SSL là viết tắt của từ (Sercure Socket Layer), đây là một tiêu chuẩn của công nghệ bảo mật, truyền thông mã hoá giữa máy chủ Webserver và trình duyệt.
- Tiêu chuẩn này hoạt động đảm bảo rằng các dữ liệu truyền tải giữa máy chủ và trình duyệt của người dùng đều riêng tư và toàn vẹn.
- Hiện tại thì SSL là tiêu chuẩn bảo mật cho hàng triệu website trên toàn thế giới, nó bảo vệ dữ liệu truyền đi trên môi trường internet được an toàn.
## Một số thuật ngữ
### CA (Certificate Authority)
- Đây là một tổ chức phát hành các chứng thực cho các loại chứng thư số cho người dùng, doanh nghiệp, máy chủ, mã code, phần mềm.
- CA đóng vai trò là một bên thứ ba (được cả hai bên tin tưởng) để hỗ trợ cho quá trình trao đổi thông tin an toàn.
### Domain Validation (DV SSL)
- Đây là loại chứng thư số chứng thực cho Domain Name-Website. Khi Website sử dụng DV SSL thì sẽ được xác thực tên domain, website đã được mã hoá an toàn khi trao đôi dữ liệu.
### Organization Validation (OV SSL)
- Chứng thư số SSL chứng thực cho Website và xác thực doanh nghiệp đang sở hữu website đó.
### Extended Validation 

## Cấu hình Https
Bước 1: Cài đặt module `mod_ssl` và các gói `openssl`.
```
yum install -y mod_ssl openssl
```
- Lệnh này sẽ tạo ra một file cấu hình chính:
`/etc/httpd/conf.d/ssl.conf`
Bước 2: Tạo thư mục chứa `server key` và `certificate`
```
mkdir /etc/httpd/ssl
```
Bước 3: Tạo SSL key và certificate
```
openssl req -x509 -nodes -day 365 -newkey rsa:2048 -keyout /etc/httpd/ssl/apache.key -out /etc/httpd/ssl/apache.crt
```
- Trong đó 
  - `openssl`: là công cụ cơ bản để quản lý OpenSSL certificates, keys, và các file khác.
  - `req -x509`: tuỳ chọn sử dụng chuẩn `x509`. Đây là một chuẩn kiến trúc mã hoá khoá công khai mà **SSL** sử dụng cho việc quản lý **key** và **certificates**.
  - `-nodes`: tuỳ chọn bỏ qua bảo mật bầng cách nhập **passphrase**. (Nếu không có tuỳ chọn này thì ta sẽ phải nhập lại 1 đoạn **passphrase** mỗi lần khởi động lại Server)
  - `-day 365`: tuỳ chọn thời gian hết hạn cho **key** và **certificate**.
  - `newkey rsa:2408`: tuỳ chọn tạo **key** và **certificatte** cùng 1 lúc. `rsa` là thuật toán mã hoá key
  - `keyout`: khai báo nơi lưu **private key**
  - `out`: khai báo nơi lưu **certificate**
- Sau khi thực hiện lệnh, 1 bảng prompt hiện ra yêu cầu nhập thông tin của Website:
Bước 4: Thêm virtual host cho website https:
```
vi /etc/httpd/conf.d/ssl.còn
```
  - Dòng 59, bỏ dấu `#` ở đầu dòng
  - Dòng 100, khai báo nơi lưu **certificate**
  - Dòng 107, khai báo nơi lưu **private key**
Bước 5: Bật firewall cho phép dịch vụ https 
```
firewall-cmd --add-service=https --permanent
firewall-cmd --reload
```
Bước 6: Khởi động lại dịch vụ httpd
```
systemctl restart httpd
```
- Truy cập trang web bằng cách nhập địa chỉ ip, 