> # Web Server (Máy chủ web)
- Là khái niệm để chỉ phần mềm chạy trên máy chủ, hoặc một phần cứng dành riêng để chạy các phần mềm hoặc trang web, từ đó cung cấp các dịch vụ World Wide Web. 
- Web server lưu trữ các thành phần để tạo nên một website (ví dụ: HTML, images, CSS và javascript,...) và gửi chúng đến người dùng cuối khi nhận được các yêu cầu (request) thông qua giao thức HTTP và 1 số giao thức khác như SMTP và FTP
- Một webserver thường lưu trữ một hoặc nhiều các trang web sử dụng chung một tài nguyên phần mềm và phần cứng được gọi là Virtual Hosting.
# Apache
- Tên đầy đủ là Apache HTTP Server
- Là phần mềm web server miễn phí mã nguồn mở (sử dụng giấy phép Apache License 2.0), được điều hành và phát triển bởi **Apache Software Foundation**
- Chiếm 46% thị phần websites trên toàn thế giới hoạt động trên hầu hết các hệ điều hành như Window, Linux, Apple Mac OS, Unix,...
# Ngnix
- NGINX là một phần mềm webserver tương tự Apache server. 
- NGINX hiệu quả hơn Apache, nó có thể làm được những thứ như load balancing, HTTP caching, hay sử dụng như một reverse proxy. 
# Cài đặt Apache đơn giản
- Bước 1: Cài đặt repo Epel (Extra Packages for Enterprise Linux- một repo cung cấp nhiều gói add-on package mà chúng ta thường dùng cho các bản Linux bao gồm CentOS, RHEL)
` yum install -y epel-release`
- Bước 2: Cài gói httpd: `yum install -y httpd`
- Bước 3: Khởi động Apache `systemctl start httpd`
- Bước 4: Đặt dịch vụ Apache để bắt đầu khi hệ thống khởi động `systemctl enable httpd`. Sau đó gõ lệnh `systemctl status httpd` để xem trạng thái của nó. Nếu `active` tức là http đã đươc khởi động thành công.
![](https://imgur.com/rhrLeT8.png)
- Bước 5: Cấu hình Firewall để cho phép truy cập Apache
` firewall-cmd --permanent --add-service=http`.
- Bước 6: Reload firewall để áp dụng thay đổi bằng lệnh `firewall-cmd --reload`
- Lúc này, khi mở một trình duyệt và gõ địa chỉ ip của máy chủ, ta sẽ thấy trang web xuất hiện. Đây mới chỉ là trang web để kiểm tra dịch vụ http có cài đặt thành công hay chưa. Để tạo nên một website hoàn chỉnh cần phải có thêm nhiều thứ khác kết hợp.

- 