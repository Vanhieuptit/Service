# Domain Name System
- Là một hệ thống phân giải tên miền mà chúng ta đang sử dụng, ở dạng ký tự sang một địa chỉ Ip dạng số tương ứng với tên miền đó và ngược lại
- DNS hoạt động theo mô hình phân cấp và phân tán trên khắp địa cầu. Tức là không có máy chủ nào chứa toàn bộ ánh xạ cho các máy tính trên Internet. Thay vào đó, các ánh xạ tên miền sẽ được phân bộ giữa các máy chủ DNS.
- Có ba lớp máy chủ DNS
  - Root DNS servers
  - Top level domain-TLD
  - DNS thẩm quyền
![](https://imgur.com/wajTiNP.png)
## Cách thức hoạt động
- Đầu tiên, máy client sẽ kết nối với một máy chủ gốc, máy chủ gốc sẽ trả về những địa chỉ của các máy chủ TLD cho tên miền mức cao nhất là .com. 
- Sau đó máy khách kết nối tới một trong những máy chủ TLD, máy chủ TLD này sẽ trả về địa chỉ IP của máy chủ thẩm quyền amazon.com
- Cuối cùng, máy client sẽ kết nối tới các máy chủ thẩm quyền cho amazon.com, máy chủ DNS này trả về địa chỉ IP cho tên domain là www.amazon.com
### Root DNS server
- Trong mạng Internet hiện nay có 13 máy chủ DNS root đặt hầu hết ở bắc mỹ
### Top level domain (TLD)
- Là những máy chủ chịu trách nhiệm về các miền mức cao như com, org, net, edu và gov vầ tất cả các miền mức cao của quốc gia như us, uk, fr, cn, jp và vn.
- Công ty Network Solutions duy trì các máy chủ TLD cho tên miền mức cao com và công ty Educause duy trì các máy chủ TLD tên miền mức cao edu.
### DNS thẩm quyền
- Bất cứ tổ chức nào có các máy truy cập công cộng (như máy chủ web và máy chủ thư điện tử) trên Internet phải cung cấp các bản ghi DNS có khả năng truy cập ra công chúng, với ánh xạ tên của các trạm chủ này sang địa chỉ IP. 
- Máy chủ DNS thẩm quyền của tổ chức sẽ chứa những bản ghi DNS này. Tổ chức có thể lựa chọn triển khai máy chủ DNS thẩm quyền riêng để giữ các bản ghi này: hoặc tổ chức đó có thể phải trả tiền để lưu trữ các bản ghi trong máy chủ DNS thẩm quyền của một nhà cung cấp dịch vụ nào đó.
- Hầu hết các trường đại học và những công ty lớn đều triển khai và duy trì máy chủ DNS thẩm quyền sơ cấp và thứ cấp(dự phòng) của riêng mình.
### Local DNS
- Mỗi ISP như ISP của trường đại học, viện nghiên cứu, công ty hay là một ISP khu vực dân cư - đều có một máy chủ DNS cục bộ tới một ISP, ISP cung cấp các địa chỉ IP của một hoặc nhiều máy chủ DNS cục bộ cho trạm chủ đó (thường là qua DHCP)