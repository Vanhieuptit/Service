I. Cài phần mềm cần thiết
```
yum install zip unzip -y
```
II. Backup và khôi phục source code
2.1) Backup source code
Để backup các file và folder trong source code của website chúng ta sẽ sử dụng lệnh zip với cấu trúc như sau:
```
zip [tùy chọn] [tên-file-cần-tạo] [thư-mục-cần-nén]
```
Trong đó, mục [tùy chọn] sẽ có rất nhiều tùy chọn nhưng hầu hết mình chỉ sử dụng một tùy chọn đó là -r: nghĩa là sẽ nén luôn cả toàn bộ file trong các thư mục con của thư mục cần nén.

Ví dụ ta có source code tại đường dẫn VirtualHost là /home/webdata/web1/public_html và mình cần nén lại toàn bộ file và thư mục trong đó thành một file tên là web1.zip thì sẽ viết như sau:
```
zip -r web1.zip /home/webdata/web1/public_html/*
```
2.2) Khôi phục source code từ file dự phòng
Để khôi phục dữ liệu hay còn gọi là xả nén dữ liệu dự phòng ra, bạn có thể sử dụng lệnh unzip nếu file đó là .zip mà bạn đã làm ở cách trên. Cấu trúc lệnh unzip là như sau:
```
unzip [tùy-chọn] [tên-file] -d [tên-đường-dẫn-cần-lưu]
```
Trong phần tùy chọn, mình hãy sử dụng nhất tùy chọn -o để ghi đè lên các file cũ trong thư mục cần giải nén nếu có.
III. Sao lưu & Phục hồi database
3.1) Backup database
Để backup database, chúng ta sẽ sử dụng lệnh mysqldump theo cấu trúc:
```
mysqldump -u [tên database username] -p [tên database cần backup] > [tên file cần lưu].sql
```
Ví dụ mình có 1 database tên là dulieu1 với database username là dulieu1_user và cần backup ra một file tên là databk.sql thì viết lệnh như sau:
```
mysqldump -u dulieu1 -p dulieu1_user >databk.sql
```
Nó sẽ hỏi mật khẩu của database user, bạn chỉ cần nhập vào là xong. Còn nếu bạn không muốn gõ mật khẩu sau khi chạy lệnh thì viết -p123456, 123456 là mật khẩu database.
Backup xong thì file .sql sẽ được lưu ở thư mục hiện tại mà bạn đang truy cập, muốn nó tự chuyển đi ra thư mục khác khi backup xong thì chỉ cần thêm đường dẫn cụ thể ở phần tên file .sql cần lưu.

3.2) Khôi phục database
Để khôi phục database ta không sử dụng mysqldump mà sẽ sử dụng lệnh mysql với cúa trúc:

mysql -u [tên database username] -p [tên database cần được khôi phục] < [tên file .sql cần khôi phục]
Ví dụ:
```
mysql -u root -p123456 newdatabase < databk.sql
```
Lưu ý là nếu database bạn đang có dữ liệu rồi thì tốt nhất hãy xóa data đó đi và tạo lại một database mới rồi hẵng import vì nó không hỗ trợ ghi đè.