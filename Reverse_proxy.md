
> # Khái niệm proxy
- Proxy được gọi là một máy chủ trung gian làm nhiệm vụ chuyển tiếp thông tin và kiểm soát tạo sự an toàn cho việc truy cập Internet của các máy khách. 
- Proxy có một địa chỉ IP và một port truy cập cố định.
## Chức năng của proxy
### Đối với công ty
- Một số hãng hoặc công tuy sử dụng proxy với mục địch
  - Giúp nhiều máy tính của công tuy truy cập internet thông qua một máy tính đại diện cùng với tài khoản truy cập nhất định
![](https://imgur.com/tgebVi1.png)
- Chỉ duy nhất máy proxy này cần moderm và account để truy cập internet, các máy client muốn truy cập internet qua máy này chỉ cần nối mạng LAN tới máy Proxy và truy cập địa chỉ yêu cầu.
- Những yêu cầu của người sử dụng sẽ qua proxy server thay thế cho server thật sự mà người sử dụng cần giao tiếp, từ proxy này, công ty sẽ có thể kiểm soát được mọi giao tiếp từ trong công ty ra ngoài Internet và từ Internet vào máy của công ty.
- Sử dụng proxy có thể cấm nhân viên truy cập vào những địa chỉ web không cho phép, cải thiện tốc độ truy cập nhờ vào sự lưu trữ cục bộ trong bộ nhớ của proxy server và giấu định danh địa chỉ của mạng nội bộ gây khó khăn cho việc thâm nhập từ bên ngoài vào các máy của công ty.
### Đối với các nhà cung cấp đường truyền internet
- Do internet có nhiều lượng thông tin mà theo quan điểm của từng quốc gia, từng chủng tộc hay địa phương mà các nhà cung cấp dịch vụ internet khu vực đó sẽ phối hợp sử dụng proxy với kỹ thuật tường lửa để tạo ra một bộ lọc gọi là `firewall proxy` nhằm ngăn chặn các thông tin độc hại hoặc trái thuần phong mỹ tục đối với quốc gia, chủng tộc hay địa phương đó.
## Forward proxy là gì?
- Máy khách có thể sử dụng forward proxy để vượt qua các hạn chế của tường lửa nhằm truy cập các trang web bị chặn bởi trường học, chính phủ, công ty,...
- Nếu một trang web chặn một dải IP truy cập web thì một người trong dải IP đó có thể sử dụng forward proxy để ẩn IP thực của khách hàng để người đó có thể truy cập trang web và có thể để lại một số bình luận, spam. Có một số dịch vụ proxy trả phí có nhiều hệ thống proxy trên khắp thế giới để chúng có thể thay đổi địa chỉ IP của bạn mỗi khi bạn truy cập một trang web mới và điều này khiến quản trị viên web khó có thể phát hiện
- Vào những năm 1990, trước khi NAT được tích hợp vào bộ định tuyến mạng, forward proxy là cách để nhiều máy tính trong cùng một mạng truy cập Internet
- Forward proxy cũng có thể hoạt động như một máy chủ bộ nhớ cache trong mạng nội bộ. Nếu một tài nguyên được tải xuống nhiều lần thì proxy có thế lưu nội dung trên máy chủ vào bộ đệm để lần sau khi máy tính khác tải cùng một nội dung, proxy sẽ gửi nội dung đã được lưu trữ trước đó trên máy chủ đến máy tính.
# Reverse proxy server là gì?
- Là một máy chủ đứng đằng trước các máy chủ web và thực hiện nhiệm vụ chuyển tiếp các yêu cầu của máy khách.
- Các proxy ngược thường được triển khai để giúp tăng cường bảo mật, hiệu suất và độ tin cậy (của phiên duyệt web hoặc ứng dụng mạng bất kỳ)
## Một số chức năng của proxy server
![]()
- Các Reverse Proxy có thể ẩn server gốc
- Cân bằng tải (load balancing): một trang web phổ biến có hàng triệu người dùng mỗi ngày có thể không thể xử lý tất cả lưu lượng truy cập trang web đến nó bằng một máy chủ gốc duy nhất. Thay vào đó, trang web có thể được phân phối giữa một nhóm các máy chủ khác nhau, tất cả đều xử lý các yêu cầu cho cùng một trang web. 
- Bảo vệ tránh sụp trang web khi có cuộc tấn công - Với Reverse proxy, một trang web hoặc dịch vụ không bao giờ cần tiết lộ địa chỉ ip của máy chủ gốc. Điều này khiến những kẻ tấn công khó thực hiện một cuộc tấn công có chủ địch chống lại họ, chẳng hạn như tấn công DDoS. Thay vào đó, những kẻ tấn công chỉ có thể nhắm mục tiêu vào Reverse proxy
- Cân bằng tải trên máy chủ toàn cầu - Chức năng này dành cho một số trang web có thể được phân phối trên một số máy chủ trên toàn cầu và proxy ngược sẽ gửi máy khách đén máy chủ gần nhất về mặt địa lý với họ. Điều này làm giảm khoảng cách mà các yêu cầu và phản hồi , giúp giảm thiểu thời gian tải trang.
- Bộ nhớ đệm (caching)- Một Reverse proxy cũng có thể lưu nội dung vào bộ nhớ cache, giúp hiệu suất nhanh hơn.
- Mã hoá SSL - mã hoá thông tin liên lạc SSL cho từng máy khách có thể tốn kém về mặt tính toán đối với máy chủ gốc. Một proxy ngược có thể được cấu hình để giải mã tất cả các yêu cầu đến và mã hoá cho tất cả các phản hồi gửi đi, giúp giải phóng các tài nguyên có giá trị trên máy chủ gốc.
## Cấu hình Reverse Proxy
- 