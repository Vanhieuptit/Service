# 1. Tổng quan
- Quản trị viên mạng / hệ thống điển hình chịu trách nhiệm quản lý một hoặc nhiều mạng con trong mạng được kiểm soát. Ví dụ, khi một phân đoạn mạng LAN được gán mạng cho /24, tổng số địa chỉ IP là 254 địa chỉ có thể được sử dụng cho các mục đích khác nhau. 
- Để theo dõi địa chỉ IP nào được gán cho máy chủ nào, cần có một dữ liệu quản lý. Các dễ nhất để làm điều đó là duy trì một bảng tính ghi lại thông phân bổ địa chỉ IP. Cách làm này thì phù hợp cho một mạng nhỏ chỉ có 1 quản trị viên. Tuy nhiên, việc dựa vào một bảng dữ liệu có sẵn không thuận tiện và dễ xảy ra lỗi với nhiều mạng lớn. 
- Một cách để quản lý phân bổ địa chỉ IP có hệ thống hơn là sử dụng công cụ quản lý địa chỉ IP dựa trên Web. Không chỉ có thể truy cập công cụ dựa trên web từ mọi nơi mà cơ sở dữ liệu phụ trợ còn đảm bảo rằng tất cả các cập nhật cho cơ sở dữ liệu được đồng bộ hóa và áp dụng đúng cách trong thời gian thực. 
- PhpIPAM là một trong những phần mềm mã nguồn mở quản lý địa chỉ IP tốt nhất được sử dụng bởi một số dịch vụ web hosting tốt nhất. Đây là một ứng dụng dựa trên các tính năng của PHP, cung cấp giao diện web hiện đại và hữu ích để quản lý địa chỉ IP. Vì vậy, đây là một lựa chọn lý tưởng cho cả người mới cũng như quản trị viên website đã có kinh nghiệm.
# 2. Điều kiện cần có để cài đặt
Để ứng dụng phpIPAM chạy được hoàn hảo trên Centos server, cần phải cài đặt các ứng dụng dưới đây
- PHP và các mô-đun PHP bắt buộc
- Apache web server
- Mariadb database
# 3. Các bước cài đặt phpIPAM trên Centos7
- Cài đặt và bật dịch vụ httpd, mariadb
```
yum install -y httpd
yum install -y mariadb-server
```
```
systemctl start httpd
systemctl enable httpd
systemctl start mariadb
systemctl enable mariadb
```
- Cài PHP và module
```
yum install -y php php-cli php-gd php-common php-ldap php-pdo php-pear php-snmp php-xml php-mysql php-mbstring git
```
- Sửa file http.conf
```
vi /etc/http/conf/http.conf
```
- Sửa dòng `DocumentRoot "/var/www/html"` thành `DocumentRoot "/var/www/phpipam"`
- Bắt đầu từ dòng 131 sửa lại như sau
![](https://imgur.com/0QvqyZc.png)

- cd vào thư mục /var/www/ sau đó clone phpipam trên kho bằng câu lệnh
```
cd /var/www
git clone https://github.com/phpipam/phpipam.git
cd phpipam
git checkout 1.3
chown -R apache:apache /var/www/phpipam
chmod -R 777 /var/www/phpipam
cp config.dist.php config.php
```
- Tạo một database với user và password trùng thông tin lưu trong file config.php
```
mysql -u root -p
```
```
create database phpipam;
create user 'phpipam'@'localhost' identified by 'phpipamadmin';
grant all privileges on phpipam.* to 'phpipam'@'localhost';
flush privileges;
```
- Giao diện sau khi cài đặt hiện lên như sau
![](https://imgur.com/PkOH9L8.png)
- Ta cần tắt SElinux để có thể Scan subnet.
# 4.Sử dụng cơ bản
### Tìm hiểu tổng quan giao diện
- Bảng thống kê về dữ liệu.
  - Number of Sections: số mục làm việc liên quan đến Subnet, ở đây đang là 3 sections. Ta có thể xem chi tiết tại đây
![](https://imgur.com/WPntmAG.png)
  - Number of Subnet: tổng số mạng con đang có trong dữ liệu quản lý
  - Number of IPv4 addresses: tổng số địa chỉ ip đang tồn tại trong CSDL
  - Number of IPv6 addresses: tổng số địa chỉ ip version 6
  - Number of Devices: tổng số thiết bị đang trong danh sách quản lý(ví dụ router, switch,...)
  - Number of Location: tổng số khu vực đang quản lý (ví dụ IDC A, IDC B)
  - Number of Rack: tổng số rack
  - Number of users: 
- Biểu đồ xếp hạng số IP có trong danh sách quản lý theo từng Sections
![](https://imgur.com/BA6orWi.png)
- Ta truy cập vào mục sections TestLab, nó bao gồm các subnets mà ta đã thêm vào để quản lý
![](https://imgur.com/Etv4e7p.png)
- Để thêm 1 subnet, ta chọn Add Subnet và thiết lập các thông số 
![](https://imgur.com/pbuOngi.png)
  - Subnet: địa chỉ mạng cần quản lý. Ví dụ 192.168.50.0/24
  - Descripton: mô tả thêm cho mạng
  - VLAN: khai báo mạng nằm trong VLAN nào
  - Devices: khai báo dòng thiết bị
  -  
### Tạo một Sections
- Bắt đầu quản lý mạng con và địa chỉ IP bằng cách thêm một Sections
  - Bước 1: Nhấp vào Adminstration > Sections
![](https://imgur.com/qCMda78.png)
  - Bước 2: Bấm vào `Add Section`, bây giờ chúng ta có thể đặt tên cho section 
![](https://imgur.com/KjvRtDm.png)
### Tạo mạng con
- Tiếp theo, chúng ta thêm một mạng con mới VD. `172.16.1.0/24` trong phần `Our Network` bằng cách bấm vào `Our Network` > `Add Subnet`.
![](https://imgur.com/8dm1JW5.png)
- Bây giờ chúng ta có thể dễ dàng thêm địa chỉ IP trong mạng con. Một phương pháp thêm địa chỉ IP là thêm từng địa chỉ một.
- PHP IPAM cung cấp một phương pháp thay thế để quét tất cả các máy chủ và thêm chúng tự động. Nó có thể quét mạng con cục bộ nằm trong cùng một miền quảng bá, cũng như các mạng con từ xa có thể truy cập thông qua định tuyến. Sau khi chọn mạng con, hãy nhấp vào `scan subnet for new hosts` để quét địa chỉ IP như hình dưới đây.
![](https://imgur.com/vmtY8Mu.png)
- Sau khi quá trình quét được thực hiện, các địa chỉ IP được phát hiện có thể được thêm vào cơ sở dữ liệu bằng cách nhấp vào `Add discovered hosts`.
### Thêm người dùng và nhóm
- Đầu tiên, chúng tôi sẽ tạo một nhóm có `READ`/`WRITE` quyền đối với phần này `Our network`. Điều này có thể được thực hiện bằng cách chọn `Administration` > `Group`>`Create Group`.
![](https://imgur.com/V4YkGcB.png)
- Bây giờ nhóm đã được tạo, chúng tôi sửa đổi quyền của phần bằng cách chọn `Administration` > `Sections`, và sau đó chỉnh sửa phần.
![](https://imgur.com/undefined.png)

![](https://imgur.com/tTmf1l5.png)
- Chúng tôi sẽ tạo một người dùng có tên `user1` sau đó thêm người dùng vào nhóm `Demonstration group` để nó kế thừa tất cả các quyền cần thiết từ nhóm. Bằng cách nhấp vào `Administration` > `Users` > `Create user`.
 ![](https://imgur.com/CnWdQ1n.png)
 - Bây giờ chúng ta có thể đăng nhập với tư cách người dùng này và thêm / sửa đổi địa chỉ IP trong phần `Our network`.
