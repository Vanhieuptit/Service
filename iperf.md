# Lệnh iperf 
- Iperf là một công cụ miễn phí, dùng để đo lường lượng dữ liệu mạng (throughput) tối đa mà một server có thể xử lý. Công cụ này rất hữu ích để truy tìm ra các vấn đề đối với hệ thống mạng bởi iperf có thể xác định được server nào không xử lý được lượng dữ liệu mạng mà người quản trị mong đợi.
- Cài đặt trên Centos7
```
yum install epel-release -y
yum install iperf -y
```
- Các option:
  - `-c`: chỉ ra địa chỉ IP của server để iperf kết nối đến
  - `-f`: chỉ ra định dạng của kết quả hiển thị (bps, Kbps,...)
  - `-i`: thời gian lấy mẫu để hiển thị kết quả tại thời điểm đó ra màn hình
  - `p`: định ra cổng để nghe, mặc định không sử dụng tham số này là cổng 5001
  - `-u`: sử dụng giao thức UDP, mặc định iperf sử dụng TCP
  - `-P`: chỉ ra số kết nối song song được tạo, nếu là Server mode thì đây là giới hạn số kết nối mà server chấp nhận.
  - `-b`: định ra băng thông tối đa có thể truyền, chỉ sử dụng với UDP, client mode.
  - `-t`: tổng thời gian của kết nối, tính bằng giây
  - `-M`: max segment size
  - `I`: Buffer size
  - `-w`: trường windows size của TCP
# Sử dụng 
- Iperf phải được cài đặt trên hai máy chủ sử dụng cho việc kiểm tra network throughput. Trong trường hợp bạn sử dụng Iperf để kiểm tra giữa máy chủ tại vHost và máy tính cá nhân của bạn, bạn phải cài đặt Iperf trên máy tính của bạn. 
- Bạn nên sử dụng Iperf để kiểm tra giữa máy chủ của bạn và một máy chủ đặt tại vị trí khác để có đánh giá chính xác nhất bởi kết quả của việc kiểm tra có thể bị tác động bởi sự giới hạn của các nhà mạng.
# Thực hiện bài test với Iperf
- Mô hình bài test
![](https://imgur.com/sq5RGMF)
- IP server 14.225.16.165
- IP client 14.225.16.166
- Sử dụng TCP: cả máy server và client đều cần cài iperf. Nếu sử dụng tham số cổng (-p) thì trên cả Server và client đều phải giống cổng nhau.
- Trên server gõ lệnh `iperf -s`
- Trên client thực hiện đẩy gói TCP tới server, kiểm tra traffic network trên card mạng của client. Thực hiện đẩy iperf TCP.
```
iperf -c 14.225.16.165
```
- Kết quả trả về
![](https://imgur.com/FqfWoEz.png)
- Hiển thị kết quả test 4s một lần trong vòng 20s dùng lệnh
```
iperf -c 14.225.16.165 -i 4 -t 20
```
UDP 
- Để sử dụng gói tin UDP để kiểm tra sử dụng thêm option `-u` trên cả máy client và server.
- Trên server
```
iperf -s -u
```
- Trên client
```
iperf -c 14.225.16.165 -u
```
![](https://imgur.com/FqfWoEz.png)
Ta có thể thấy bandwidth khi sử dụng gói tin UDP và TCP khác nhau là rất lớn bởi mặc định iperf giới hạn bandwidth cho kết nối UDP là 1Mb/s. Ta có thể thay đổi thông số này bằng cách sử dụng option `-b`.
```
iperf -c 14.225.16.165 -u -b 100M
```
- Để kiểm tra tải của server ta sẽ đẩy liên tục các gói tin UDP đến server để kiểm tra. Thay đổi băng thông tối đa và quan sát băng thông tối đa mà nó đạt được.
```
iperf -c 14.225.19.165 -u -b 2G -i 1 -t 10
```
Ta thấy băng thông vẫn có thể tăng lên tối đa được tiếp
![](https://imgur.com/FqfWoEz.png)
# Lệnh stress
- Là lệnh được sử dụng để tạo ra khối lượng công việc để kiểm tra hiệu năng của phần cứng.
- Để có thể kiểm tra ta sử dụng 2 terminal để kiểm tra. Cái 1 là dùng chạy câu lệnh `stress` và cái còn lại dùng lệnh `htop` để theo dõi.
- Trước tiên cài đặt công cụ stress
```
yum install stress
```
- Một số ví dụ cụ thể 
## Test CPU
- Để test CPU ta sử dụng option `-c`
Để thấy sự khác biệt ta dùng câu lệnh `htop` trước khi dùng câu lệnh `stress`
- CPU ban đầu
![](https://imgur.com/ejgHPNl.png)
- CPU sau câu lệnh `stress -c 1` : tạo ra một tiến trình với hiệu suất cao để kiểm tra CPU
1[](https://imgur.com/Jw0cqTT.png)
- Tiếp tục tăng số process lên để kiểm tra ta thấy
![](https://imgur.com/tpwkzR2.png)
Lúc này mỗi process chỉ còn sử dụng 50% CPU nên tốc độ sẽ giảm đi tương ứng
# Test RAM
Để test hiệu năng của RAM ta dùng option `-m`
Hiệu năng RAM ban đầu
![](https://imgur.com/S5NPkNK.png)
```
stress -m 2
```
- Với câu lệnh này, stress tạo ra 2 process mỗi process sẽ sử dụng `256` RAM để kiểm tra. 
![](https://imgur.com/WoGwn4E.png)
- Nếu không muốn dùng số RAM mặc định cho mỗi process là `256M` ta có thể chi ra bằng cách dùng thêm option `--vm-bytes`. Bạn cũng có thể đặt lệnh test được chạy trong khoảng thời gian bao lâu bằng cách thêm option `-t`.
```
stress -m 2 --vm-bytes 500M -t 60
```
- Câu lệnh này sẽ sinh ra 2 tiến trình và mỗi tiến trình sẽ sử dụng 500M RAM và chạy trong 60s.
# Test disk
- Để test disk ta dùng option `-d`
- VD câu lệnh
```
stress -d 1 --hdd-bytes 2G -t 1m
```
- Câu lệnh này sẽ ghi một file có dung lượng 2G và disk và thực hiện trong vòng 1 phút.
- Kết quả trả về như sau
```
[root@test2 ~]# stress -d 1 --hdd-bytes 2G -t 1m
stress: info: [4951] dispatching hogs: 0 cpu, 0 io, 0 vm, 1 hdd
stress: info: [4951] successful run completed in 63s
```
Nếu không chỉ ra dung lượng file bằng cách thêm option `--hdd-bytes` thì mặc định file có dung lượng là 1G. Các file này sau lệnh stress nó sẽ tự động được dọn dẹp nên các bạn không lo các file rác được tạo ra 