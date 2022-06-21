# Tổng quan file cấu hình
- Về tổng quan, file config chính sẽ được sắp xếp theo cấu trúc cây, xác định bởi tập hợp hay dấu `{}`. 
- Các thành phần được xác định bởi dấu đó được gọi là "context"
## Các context phổ biến
- Main Context (Global Context)


# Xử lý client request trong webserver
## Nginx Block Configurations
- Nginx phân chia config trong nhiều block khác nhau tạo thành cấu trúc hình cây. 
- Mỗi lần có request được gửi lên server, nginx sẽ xác định config block nào sẽ được sử dụng để handle request
- Một khối block để handle request có dạng như sau
```
server {
    location {
    
    }
}
```
- Một server Block là một tập hợp của nhiều cấu hình nginx quyết định hướng xử lý cho các request khác nhau dựa trên domain name port và IP address.
- Một location block nằm trong server block được sử dụng để định nghĩa mà nginx có thể handle request cho nhiều request với các dạng URL khác nhau.
- URL có thể được chia thành bất cứ cách nào mà admin hệ thống muốn để phân loại request.
## Cách nginx quyết định server block nào sẽ handle request
- Nginx cho phép khai báo server trong config thành những khối block riêng biệt cho từng virtual host thì ta cần phải xác định được khi nào thì request nào sẽ được xử lý ở đâu. NGINX thực hiện việc này thông qua 2 thuộc tính trong config đó là listen directice và server_name directive.
### Quá trình Nginx tìm ra server web nào mà request sẽ được map đến.
- Đầu tiên, Nginx sẽ nhìn vào địa chỉ IP và port của request, Nginx sẽ đối chiếu với mỗi listen directive của mỗi server block để tìm ra block nào sẽ giải quyết request này.
- Thông thường **listen directive** sẽ được định nghĩa là **IP** hay **port** mà server sẽ trả lời response
- Nếu không khai báo thì nginx sẽ nghe default (0.0.0.0:80) hoặc (0.0.0.0:8080). Tóm lại là reponse sau khi xử lí xong sẽ trả về response về lại cổng 80.
- Listen directive có thể được chia thành
  - Địa chỉ Ip đơn (default sẽ chạy ở cổng 80), hoặc combo bao gồm ip và port.
  - path to unix socket.
-  Trong quá trình xác định xem request sẽ được chuyển đến server block nào, nginx sẽ cố xác định thông tin từ listen directive trước theo quy tắc như sau:
  - NGINX sẽ gán giá trị mặc định cho block bị thiếu thông tin. VD: block không có listen directive sẽ sử dụng value là `0.0.0.0:80`, block chỉ set địa chỉ sẽ mặc định nghe ở cổng 80, block chỉ có mỗi port sẽ chuyển về nghe ở địa chỉ 0.0.0.0.
- Sau quá trình find theo ip và port, nginx sẽ find tiếp theo server_name directive và đối chiếu nó với filed Host trong request.
```
server {
    listen 80;
    server_name *.example.com;

    . . .

}
```
- Tóm tắt lại chức năng của listen directive là chỉ ra cổng mà nginx lắng nghe cho giao thức truyền tới. Ví dụ listen 80 nghĩa là tất cả những request gửi tới bằng giao thức http sẽ được xử lí tại đây. còn server_name directive sẽ đối chiếu với field Host trong header của request để xem request có được xử lí theo khối block server này không
## Location directive block
- Sau khi đã chọn được server block nào sẽ tiếp nhận request này thì nginx sẽ tiếp tục phân tích URL của request để tìm ra hương xử lý của request dựa vào cac block location có syntax như sau
