# Tuỳ chỉnh tham số worker_processes
- Worker_processes là tổng số processes tối đa mà Nginx sử dụng. 
- Thông thường là 1 worker processes trên một lõi CPU. Bạn có thể sử dụng lệnh sau để kiểm tra số lõi: 
```
grep processor /proc/cpuinfo | wc -l
```
- Lấy con số này sử dụng cho worker processes. 

- Worker connections ​cho biết có bao nhiêu người Nginx có thể phục đồng thời. Bạn có thể check giới hạn bộ xử lý bằng câu lệnh sau: 
```
ulimit -n
```
- Sử dụng con số này cho worker_connections. 
- Ví dụ ở đây sau khi kiểm tra ta thấy có số core là 1 và số ulimit là 1024, ta thực hiện cập nhật tham số cho file cấu hình **/etc/nginx/nginx.conf**
```
worker_processes 1;
worker_connections 1024;
``` 
- Nhưng nếu ta đặt như này
```
worker_processes 4;
worker_connections 1024;
``` 
- Thì số lượng người truy cập tối đa sẽ là 1024*4=4096
# Xoá thông tin phiên bản Nginx đang sử dụng
- Thêm dòng sau vào file cấu hình
```
server_tokens off;  

```
# Không cho phép truy cập vào thư mục ẩn
- Thêm dòng sau vào file cấu hình
```
location ~ /. {  
   access_log off;  
   log_not_found off;   
   deny all;  
}  
```
# Cache
- Một tinh chỉnh quan trọng khác đó là kích thước cache. Nếu kích thước cache quá thấp, thì Nginx sẽ phải ghi vào một file tạm thời khiến đĩa đọc và ghi liên tục. 
- **client_body_buffer_size**: xử lý kích thước cache ứng dụng client, nghĩa là kích thước của bất kỳ hành động POST nào được gửi đến Nginx.
- **client-header_buffer_size**: tương tự như chỉ thị trên nhưng chỉ thị này xử lý kích thước tiêu đề client. 1K là kích thước phù hợp
- **client_max_body_size**: kích thước tối đa được phép cho một yêu cầu của 1 client, nếu vượt quá kích thước này, thì Nginx sẽ xuất hiện lỗi 413
- **large_client_header_buffers**: số lượng và kích thước cache tối đa cho các tiêu đề client lớn.
- Ví dụ 
```
client_body_buffer_size 10K; client_header_buffer_size 1k; client_max_body_size 8m; large_client_header_buffers 2 1k; 
```
## Timeout
- Tuỳ chỉnh thời gian chờ cũng có thể cải thiện đáng kể hiệu suất
- **client_body_timeout** và **client_header_timeout**: chỉ chịu trách nhiệm khi một server sẽ chờ đợi cho một **body** hoặc **header** của client. Nếu cả nội dung hoặc tiêu đề đều không được gửi, server sẽ đưa ra lỗi 408.
- **keepalive-timeout**: ấn định thời gian chờ cho các kết nối duy trì hoạt động với client, sau khoảng thời gian thiết lập, Nginx sẽ đóng các kết nối với client sau khoảng thời gian này.
- **send_timeout** nginx sẽ ngắt kết nối sau 1 khoảng thời gian mà khách hàng không nhận được gì.
- Ví dụ
```
client_body_timeout 12; client_header_timeout 12; keepalive_timeout 15; 
send_timeout 10; 
```
## Nén gzip
- Gzip có thể giúp giảm số lượng giao dịch chuyển mạng mà Nginx thực hiện. Tuy nhiên, hãy cẩn thận khi tăng **gzip_comp_level** quá cao khiển server lãng phí chu kỳ cpu.
```
gzip             on; 
gzip_comp_level  2; 
gzip_min_length  1000; gzip_proxied     expired no-cache no-store private auth; gzip_types       text/plain application/x-javascript text/xml text/css application/xml; 
```
# Cache file tĩnh
- Có thể đặt tiêu đề hết hạn cho các file không thay đổi và được cung cấp thường xuyên. 
- Chỉ thị này được thêm vào khối server
```
location ~* .(jpg|jpeg|png|gif|ico|css|js)$ { exprires 365d; }
```
# Ghi log
