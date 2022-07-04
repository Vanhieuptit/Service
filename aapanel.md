aaPanel là một bảng điều khiển lưu trữ web mã nguồn mở để quản lý các dịch vụ lưu trữ như cơ sở dữ liệu, trang web, v.v.. Ở đây chúng ta sẽ xem cách cài đặt và thiết lập nó trên máy chủ Linux Hệ điều hành CentOS 7.1+, Ubuntu 16.04+, Debian 9.0+ chưa được cài bất cứ phần mềm control panel hoặc webserver nào.
## Cài đặt
- Tải tập lệnh mới nhất
```
wget -O install.sh http://www.aapanel.com/script/install_6.0_en.sh
```
- Chạy tập lệnh
```
bash install.sh
```
Khi nó thông báo bạn có muốn cài đặt aaPanel vào thư mục /www ngay bây giờ không. Nhập Y và nhấn nút Enter .
```
Do you want to install aaPanel to the /www directory now?(y/n): y
Do you need to enable the panel SSl ? (yes/n): yes
```
- Sau khi cài đặt hoàn tất, bạn sẽ nhận được một liên kết đăng nhập: http://SERVER_IP:7800. Ngoài ra, tên người dùng và mật khẩu sẽ ở đó để sử dụng, để truy cập vào bảng điều khiển.
## Dừng, Bắt đầu & Khởi động lại aaPanel
Dừng aaPanel:
```
service bt stop
```
## Bắt đầu aaPanel:
```
service bt start
```
## Khởi động lại aaPanel:
```
service bt restart
```
