# Kiểm tra hiệu suất của một VPS
## Check performance bằng câu lệnh 
```
curl -Lso- tocdo.net | bash
```
- Kết quả cho ra các tham số cần thiết
```
System Info
----------------------------------------------------------------------
CPU model            : Intel(R) Xeon(R) CPU E5-2683 v4 @ 2.10GHz
Number of cores      : 1
CPU frequency        : 2099.998 MHz
Total size of Disk   : 9.0 GB (6.6 GB Used)
Total amount of Mem  : 991 MB (281 MB Used)
Total amount of Swap : 1023 MB (18 MB Used)
System uptime        : 3 days, 7 hour 58 min
Load average         : 0.60, 0.17, 0.10
OS                   : CentOS 7.9.2009
Arch                 : x86_64 (64 Bit)
Kernel               : 3.10.0-1160.76.1.el7.x86_64
Virt                 : vmware
Date                 : Mon Aug 22 17:18:28 +07 2022

Disk Speed
----------------------------------------------------------------------
dd Test
I/O (1st run)        : 1.1 GB/s
I/O (2nd run)        : 1.3 GB/s
I/O (3rd run)        : 1.3 GB/s
Average              : 1262.9 MB/s
-----------------------------------
Fio Test
Read performance     : 289MB/s
Read IOPS            : 70.6k
Write performance    : 96.5MB/s
Write IOPS           : 23.6k

Speedtest
----------------------------------------------------------------------
Node Name                               IPv4 address    Download Speed
CacheFly                                204.93.150.152  58.5MB/s
Vultr, Los Angeles, CA                  108.61.219.200  13.2MB/s
Vultr, Seattle, WA                      108.61.194.105  12.5MB/s
Linode, Tokyo, JP                       139.162.65.37   8.49MB/s
Linode, Singapore, SG                   139.162.23.4    38.7MB/s
Softlayer, HongKong, CN                 119.81.130.170  35.0MB/s
VNPT, Ha Noi, VN                        113.164.24.102  220MB/s
VNPT, Da Nang, VN                       113.164.16.66   19.7MB/s
VNPT, Ho Chi Minh, VN                   113.164.8.250   13.1MB/s
Viettel Network, Ha Noi, VN             27.68.226.129   172MB/s
Viettel Network, Da Nang, VN            27.68.201.1     37.2MB/s
Viettel Network, Ho Chi Minh, VN        27.68.239.33    66.6MB/s
FPT Telecom, Ha Noi, VN                 118.70.115.12
FPT Telecom, Ho Chi Minh, VN            118.70.71.216
----------------------------------------------------------------------

```
## 1 CPU
- Kiểm tra CPU trên hệ thống Linux:
Bạn có thể xem thông tin của CPU trên VPS bằng cách xem nội dung của file /proc/cpuinfo bằng lệnh sau:
```
cat /proc/cpuinfo
```
## RAM
```
free -m
```
## 2 Ổ cứng
- Lệnh ``dd`` thông dụng.
```
dd if=/dev/zero of=test bs=64k count=16k conv=fdatasync
```
- Câu lệnh này thực hiện ghi 1 file 1GB xuống hệ thống với block size là 64K.

- Tại sao cần test ổ cứng?
Ví dụ: một tờ báo Vietnamnet có bài viết nào đó (ví dụ Thông tin dự báo thời tiết ngày 19/8/2022) có số lần ghi là 1(biên tập viên thực hiện việc đăng bài lênh hệ thống) là 1 lần hoặc có thể chỉnh sửa bài chỉ vài lần nhưng số lượng người đọc sẽ là rất nhiều, có thể từ vài trăm đến vài chục nghìn người đọc từ ngày này qua ngày khác.
Như vậy, để kiểm tra hiệu suất ổ cứng của VPS lệnh dd không phải là câu lệnh tốt nhất và bao quát được tất cả các thông tin.
- Để chính xác chúng ta nên kiểm tra thông số IOPS và latency (độ trễ) của ổ cứng VPS của mình bằng công cụ FIO
#### Kiểm tra random IOPS với công cụ FIO
- IOPS là từ viết tắt của Input/Output Operations per Second. Đây là con số thể hiện tốc độ đọc ghi trong mỗi giây của ổ cứng. Con số này càng cao thì hệ thống hoạt động càng nhanh.
- Cụ thể hơn trong kết quả test, chúng ta sẽ kiểm tra các thông số sau:
  - Random write, random read hoặc kết hợp cả hai. Ví dụ như đối với database khi truy cập hệ thống sẽ đọc data từ ổ cứng, được hiểu là random read (accesss).
  - Block size 4KB, đây là con số lý tưởng nhất. Cụ thể như đối với các hệ thống database và source code cũng như phần lớn các hệ thống khác sẽ truy xuất lượng dữ liệu là rất nhỏ nhưng số lần yêu cầu truy xuất (read) là rất nhiều.
  - Xử lý đa nhiệm: khác với câu lệnh dd là việc xử lý diễn ra chỉ là 1 tiến trình duy nhất tuy nhiên đối với công cụ FIO sẽ kiểm tra được tốc độ khi xử lý luân phiên đồng thời. 
### Cài đặt công cụ FIO
- Cách cài 1.
```
# cd /root
# yum install -y make gcc libaio-devel || (apt-get update && apt-get install -y make gcc libaio-dev </dev/null )
# wget https://github.com/axboe/fio/archive/fio-3.13.tar.gz ; tar xf fio-3*
# cd fio-fio-3.13/
# make
```
- Cách cài 2
```
# wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
# rpm -ivh epel-release-latest-7.noarch.rpm
# yum install -y fio
```
- Sau khi hoàn thành chúng ta có thể tiến hành test.
### 1. Kiểm tra random read/write đồng thời
```
./fio --randrepeat=0 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=vhost --filename=vhost --bs=4k --iodepth=64 --size=4G --readwrite=randrw --rwmixread=75
```
- Đây là câu lệnh dùng để test IOPS của ổ cứng. Câu lệnh này sẽ tạo ra file 4GB và test theo cơ chế read và write file 4KB đồng thời tỷ lệ 75%25% (cứ 3 request read thì sẽ có 1 request write) với 64 lần thực hiện đồng thời tại một thời điểm. Tỉ lệ 3:1 rất phổ biến với các dạng database ngày nay.
- Trong quá trình chạy hệ thống sẽ hiện thông tin tổng quát theo định dạng sau:
![](https://imgur.com/bvjUS0y.png)
- Tại đây, chúng ta sẽ quan tâm tới các thông số sau
  - Tốc độ đọc có IOPS = 68.8K*4 ~ 275,2MB/s
  - Tốc độ ghi có IOPS = 22.9k*4 ~ 91.6MB/s
### 2. Kiểm tra tốc độ đọc của ổ đĩa
```
fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=random_read.fio --bs=4k --iodepth=64 --size=4G --readwrite=randread
```
### 3. Kiểm tra tốc độ ghi
```
$ fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=random_write.fio --bs=4k --iodepth=64 --size=4G --readwrite=randwrite
```
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