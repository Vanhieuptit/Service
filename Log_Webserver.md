- Log là một file ghi lại tất cả các thông báo về hoạt động của cả hệ thống của các dịch vụ được triển khai trên hệ thống.
- File log thường là các file văn bản thông thường dưới dạng clear text giúp ta có thể dễ dàng đọc được nó.
- Log của webserve nằm trong thư mục /var/log/
![](https://imgur.com/P2iTYK9.png)
# Access log
- Có chức năng ghi lại những lần sử dụng, truy cập, yêu cầu đến apache server.
- File log được lưu trữ tại var/log/httpd/access_log
- Định dạng file log (LogFormat) cơ bản như sau là: %h %l %u %t %r %>s %b Refer User_agent.
- Trong đó 
  - `%h`:là địa chỉ của máy client.
  - `%l`: nhận dạng người dùng được xác định bởi identd (thường không sử dụng vì không tin cậy)
  - `%u`: tên người dùng được xác định bởi giao thức HTTP
  - `%t`: thời gian yêu cầu được nhận
  - `%r`: là yêu cầu từ người sử dụng (client)
  - `%>s`: mã trạng thái được gửi từ máy chủ đến máy khác 
  - `%b`: kích cỡ phản hồi đối với client
  - `Refer`: tiêu đề Refeer của yêu cầu HTTP (chứa URL của trang mà yêu cầu này được khởi tạo) 
  - `User_agent`: chuỗi xác định trình duyệt
- Ví dụ:
```
157.230.216.203 - - [19/Jun/2022:14:14:04 +0700] "GET /ab2g HTTP/1.1" 400 226 "-" "-"
```
![](https://imgur.com/uEjaqno.png)
  - `157.230.216.203`: là địa chỉ ip của máy client truy cập tới apache server
  - Hai trường `%l` và `%u` không có giá trị nên sẽ hiển thị `-`
  - `[19/Jun/2022:14:14:04 +0700]`: là thời gian nhận được yêu cầu từ client
  - `GET / ab2g HTTP/1.1`: là yêu cầu từ client
  - 400 là mã trạng thái gửi từ server đến client
  - `226`: là kích thước phản hồi lại client
  - Hai trường cuối cũng không có giá trị nên sẽ là `-` `-`
# Error log
- Chứa thông tin về lỗi mà máy chủ web gặp phải khi xử lý các yêu cầu, chẳng hạn như tệp bị thiếu
- Là nơi đầu tiên để xem xét khi xảy ra sự cố khi khởi động máy chủ hoặc với hoạt động của máy chủ vì nó thường chứa thông tin chi tiết về những gì xảy ra và cách khắc phục.
- Nơi lưu trữ file log là /var/log/httpd/error_log (đối với Centos)
- Nơi lưu trữ /var/log/apache2/error.log (đối với ubuntu)
- Định danh của error log tương đối tự do về mặt hình thức nhưng 1 số thông tin quan trọng có trong hầu hết các mục log như sau:
  - Trường thứ nhất: Trường thời gian - lưu thời gian nhận được message từ apache server
  - Trường thứ 2: liệt kê mức độ nghiêm trọng của lỗi được báo cáo
  - Trường thứ 3: Địa chỉ IP của client tạo ra lỗi 
- Ví dụ
![](https://imgur.com/Fcc062B.png)
