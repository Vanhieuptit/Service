# IOPS là gì?
- IOPS là viết tắt của cụm từ Input/Output per Second, IOPS được hiểu là đơn vị đo lường cho biết tổng số lượng tác vụ Read hoặc Write được hoàn thành trong 1 giây. Hiểu đơn giản thì IOPS là số thao tác đọc ghi trong một giây của ổ cứng.
VD: 4k được 50000 IOPS nghĩa là tốc độ read/write = 50000*4K=200000K/s xấp xỉ 200MB/s. Tham số này thể hiện: SSD này sao chép được 200MB/s các file nhỏ như 4k trong thời gian 1 giây.
- IOPS thường được sử dụng cho các thiết bị lưu trữ như HDD, SSD, hoặc SAN.
- **Băng thông** (MBps) là thông số quan trọng nhất ở các thiết bị lưu trữ file. Còn đối với các thiết bị lưu trữ cho đám mây CLOUD thì IOPS quyết định độ "nhạy" và độ "nhanh" của máy ảo.
- Các nhà sản xuất thiết bị đã thông báo công khai tham số IOPS không liên quan gì đến các ứng dụng đo lường hiệu năng cả. Nên tùy theo tính chất riêng mà các bạn sẽ chọn một ứng dụng phù hợp.
# Vai trò của IOPS đối với Cloud Server.
## Tham số IOPS tỉ lệ thuận với tốc độ xử lý
- Đối với Cloud Server, tài nguyên phần cứng là nơi được chia sẻ để dùng chung với nhiều người. Do đó, muốn tốc độ xử lý nhanh thì cần có chỉ số IOPs cao, dẫn đến tác vụ sẽ được xử lý nhiều hơn. Từ đó, hiệu năng của các ứng dụng trên Cloud Server sẽ cao lên theo.
- Thông tin quan trọng nhất đối với IOPS mà bạn cần chú ý đến là tỉ lệ Read và Write (thông thường tỉ lệ này là 70% (read) và 30% (write)).
## Công thức tính IOPS và số lượng ổ cứng
- Công thức:
- Tổng IOPS = IOPS per Disk*Số ổ cứng
- IOPS thực = (Tổng IOPS * Write%)/(Raid Penalty) + (Tổng IOPS * Read %)
- Số ổ cứng = ((Read IOPS) + (Write IOPS*Raid Penalty))/ IOPS per Disk
- Bảng thông số:

|RAID Level|Capacity|IOPS|
|-|-|-|
|RAID 5|5,626 GB|821|
|RAID 6|4,822 GB|624|
|RAID 10|3.215 GB|1200|
- Ví dụ hệ thống lưu trữ của bạn dùng ổ SAS 15k. Dung lượng mỗi ổ là 900Gb. Tỷ lệ R/W tương ứng 7:3. Cấu hình RAID 10. IOPS per Disk là 176. Yêu cầu đặt ra là IOPS thực phải trên 1000. Lúc này, hệ thống của bạn chỉ cần 8 ổ cứng là đủ. Số IOPS của hệ thống lúc này là 1200.
# Latency là gì?
- Latency là thời gian ổ cứng bắt đầu thực hiện một data transfer.
- Thực chất, Latency là khái niệm về tốc độ xử lý 1 request I/O của hệ thống.
- Latency Network còn gọi là độ trễ của mạng. Hiểu đơn giản, độ trễ của tốc độ mạng ở đây là thể hiện cho sự trậm chễ phát sinh trong xử lý dữ liệu của mạng máy tính.
# Latency được tính như thế nào?
- Đơn vị đo Latency là ms.
- Có khá nhiều cách và công cụ để kiểm tra đỗ trễ của mạng. Trong đó, kiểm tra Ping và Traceroute là 2 cách kiểm tra phổ biến nhất.
- Cách đo: Độ trễ được đo bằng cách xác định thời gian cần để 1 gói dữ liệu mạng đi tới đích và trở về điểm xuất phát ban đầu.
- Latency được xem là thông số quan trọng nhất trong hệ thống lưu trữ.
- Khái niệm này rất quan trọng, một hệ thống lưu trữ chỉ có capacity 1000 IOPS với thời gian trung bình xử lý latency 10ms hoàn toàn vẫn có thể tốt hơn 1 hệ thống với 5000 IOPS nhưng latency là 50ms, nhất là đối với dịch vụ Database.

