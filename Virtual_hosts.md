# Virtual host
- Là một phương thức lưu trữ cho phép lưu nhiều tên miền khác nhau trên cùng một máy chủ Server. 
- Cho phép nhúng nhiều tên miền trên một địa chỉ IP của một Server duy nhất.
![](https://imgur.com/bm1xfUx.png)
- Có 3 dạng Virtual Host:
  - IP-based: mỗi website gán với 1 IP ảo
  - Port-based: mỗi website gán vào 1 port của IP
  - Name-based: nhiều tên miền chạy trên 1 địa chỉ IP 
## Các tạo Virtual host (theo Name-based)
- Yêu cầu: cần có 1 hệ điều hành Centos và đã cài đặt Apache.
- Mặc định, Apache sử dụng `var/www/html` làm thư mục chủ để lưu trữ dữ liệu và chứa tập tin trên Web Server. Vì vậy, cần phải tạo thư mục lưu trữ cho Virtual Host theo đúng số lượng Web thực tế cần chạy.
- Tắt SElinux để cho phép Apache có thể can thiệp sâu vào hệ thống
- **B1**: Tạo folder là để dùng lưu trữ dữ liệu của người dùng, hoặc chứa dữ liệu của người dùng khi truy cập vào web.
- VD: tạo 2 thư mục gồm 
  - /var/www/html/web1 
  - /var/www/html/web2
```
mkdir -p /var/www/html/web1
mkdir -p /var/www/html/web2
```
  - Cấp quyền đọc được chấp nhận với tất cả các file và thư mục bên trong /var/www/html/
```
chown -R apache:apache /var/www/html/
chmod -R 755 /var/www/html
```
- **B2**: Tạo ra file index.html đơn giản cho 2 trang web để kiểm tra hoạt động của Virual host.
```
echo "<center><h1>Đây là web1</h1></center>" > /var/www/html/web1/index.html
echo "<center><h1>Đây là web2</h1></center>" > /var/www/html/web2/index.html
```
- **B3**: Tạo 2 thư mục lưu trữ file cấu hình Vitrual host cho apache:
```
mkdir /etc/httpd/sites-available
mkdir /etc/httpd/sites-enabled
```
  - Trong đó: 
    - **sites-available** chứa các cấu hình Virtual host có trên hệ thống.
    - **sites-enabled** chứa các cấu hình Virtual host được kích hoạt để chạy.
- **B4** :Gõ lệnh ` vi /etc/httpd/conf/httpd.conf` và thêm dòng sau vào cuối file
` IncludeOptional sites-enabled/*.conf` sau đó lưu lại và thoát.
- Tạo file Virual host cho `web1.com` :
`vi /etc/httpd/sites-available/web1.com.conf`
- **B5**: Thêm nội dung sau vào file
``` 
<VirtualHost *:80>
  ServerAdmin admin@web1.com
  ServerName web1.com
  ServerAlias www.web1.com
  DocumentRoot /var/www/html/web1
  DirectoryIndex index.php index.html
  ErrorLog /var/www/html/web1/error.log
  CustomLog /var/www/html/web1/requests.log comnined
</VirtualHost>
```
- Trong đó:
  - **ServerAdmin**: khai báo email của quản trị viên.
  - **ServerName**: khai báo domain mà website sẽ lắng nghe.
  - **ServerAlias** (tuỳ chọn): khai báo Alias của domain, thường là www.
  - **DirectoryIndex**: loại file sẽ được tìm đến để khởi chạy.
  - **DocumentRoot**: khai báo đường dẫn chứa code của website.
  - **Error** và **CustomLog** khai báo đường dẫn lưu file của website.
- Tạo file Virtual host với Web2 cũng làm tương tự như vậy
- **B7**: Kích hoạt virtual host
 - Tạo một (symbolic link) vào thư mục **sites-enabled**
```
ln -s /etc/httpd/sites-available/web1.com.conf /etc/httpd/sites-enabled/web1.com.conf
ln -s /etc/httpd/sites-available/web2.com.conf /etc/httpd/sites-enabled/web2.com.conf
```
- **B8**: Restart Apache để lưu thay đổi `apachectl restart`
- Cuối cùng là kiểm tra hoạt động bằng cách mở trình duyệt web lên và nhập các domain.
- Tuy nhiên với bài lab này ta cần phải cấu hình Local host cho máy Window của mình để trình duyệt có thể phân giải tên miền ra địa chỉ ip của máy chủ web
  - B1: Mở notepad với quyền admin
  - B2: Tìm đến đường dẫn  `C:\Windows\System32\drivers\etc` và mở file `hosts` lên
  - B3: Thêm dòng `	192.168.12.130       web1.com web2.com` và lưu lại
![](https://imgur.com/89jYPCR.png)
- Ý nghĩa của việc cấu hình Local host ở trên là: 
 - Khi các bạn gõ địa chỉ web1.com thì máy sẽ ưu tiên tìm địa chỉ IP của domain đó trong file hosts của hệ điều hành trước tiên. Nếu không có thì nó sẽ đi tìm địa chỉ IP của domain này thông qua các DNS Server trên Internet. Vì bạn đã khai báo trong file hosts nên nó sẽ sử dụng địa chỉ IP được khai báo trong đó luôn.