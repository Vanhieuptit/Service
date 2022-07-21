# Crontab là gì?
- Crontab linux là một dịch vụ giúp thực hiện các task được lên lịch sẵn, giúp cải thiện đáng kể hiệu suất làm việc
- Giúp tự động tạo và chạy các script dựa trên lịch có sẵn, thời gian có thể lặp lại hằng ngày, hàng tuần, hàng tháng.
# Cách thức hoạt động của crontab
- Một cron schedule đơn giản là một text file. Mỗi người dùng có một cron schedule riêng, file này thường nằm ở /var/spool/cro. 
- Crontab files không cho phép bạn tạo hoặc chỉnh sửa trực tiếp với bất kỳ trình text editor nào, trừ phi bạn dùng lệnh crontab.
#### Một số lệnh thường dùng
- Tạo và chỉnh sửa file crontab
```
crontab -e file_crontab
```
- Hiển thị file_crontab
```
crontab -l file_crontab
```
- Xóa file crontab
```
crontab -r file_crontab
```
# Cài đặt crontab
- Hầu hết tất cả VPS đều được cài đặt sẵn crontab, tuy nhiên vẫn có trường hợp VPS không có. Nếu bạn sử dụng lệnh crontab -l mà thấy output trả lại -bash: crontab: command not found thì cần tự cài crontab thủ công.
- Sử dụng lệnh
```
yum install cronie
```
- Start crontab và tự động chạy mỗi khi reboot
```
service crond start
chkconfig crond on
```
# Cách sử dụng Crontab
- Cron hoạt động dựa trên các lệnh được chỉ định trong cron table(crontab).
- Mỗi người dùng, kể cả root, đều có thể có một file cron. Các file này chạy theo mặc định sẽ không tồn tại. Nhưng ta có thể tạo nó trong thư mục /var/spool/cron bằng cách dùng lệnh crontab -e. 
- Ngoài ra, lệnh này cũng có thể được dùng để chỉnh sửa một file cron. Không nên dùng vi hay vim để chỉnh sử file cron.
- Các file cron sẽ trống, nên các lệnh phải được thêm từ đầu. Ví dụ
![](https://imgur.com/iM8ZHxM.png)
- Ba dòng đầu tiên có nhiệm vụ thiết lập một môi trường mặc định. Mỗi trường phải được thiết lập phù hợp với nhu cầu của người dùng. Bởi vì cron không cung cấp một mỗi trường cụ thể nào cả. 
- Biến SHELL chỉ định shell để sử dụng khi các câu lệnh được thực thi.
- Biến MAILTO đặt địa chỉ mail nhận các kết quả của cron job.
- Biến PATH có nhiệm vụ thiết lập PATH cho môi trường.
- Dong ` 0  2  *  *  1 cd /etc/letsencrypt/ && ./certbot-auto renew && systemctl restart nginx` Được thiết lập để chạy vào 2h00 thứ 2 hàng tuần các lệnh

