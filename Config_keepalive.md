# Giới thiệu về keepalived
- Là một phần mềm định tuyến, được viết bằng ngôn ngữ C. 
- Cho phép nhiều máy tính cùng chia sẻ một địa chỉ IP ảo với nhau theo mô hình (Active-Passive)
- Khi người dùng truy cập vào dịch vụ, người dùng chỉ cần truy cập vào địa chỉ IP ảo dùng chung thay vì phải truy cập vào những địa chỉ IP thật.
## Một số đặc điểm
- Keepalived không đảm bảo tính ổn định của dịch vụ chạy trên máy chủ, nó chỉ đảm bảo rằng sẽ luôn có ít nhật một máy chủ chịu trách nhiệm cho IP dùng chung khi có sự cố xảy ra.
- Keepalive thường được dùng để dựng các hệ thống HA (High Availability) dùng nhiều router/firewall/server để đảm bảo hệ thống được hoạt động liên tục.
- Keepalive dùng giao thức VRRP (Virtual Router Redundancy Protocol) để liên lạc giữa các thiết bị trong nhóm.
# Giao thức VRRP
- Virtual router đại diện cho một nhóm thiết bị sẽ có một virtual IP và một địa chỉ MAC có dạng `00-00-5E-00-01-XX`. Trong đó, **XX** là số định danh của router ảo
- Mỗi Router ảo trong một mạng sẽ có một giá trị VRID khác nhau. Vào mỗi thời điểm nhất định, chỉ có một router vật lý dùng địa chỉ MAC ảo này. Khi có ARP request gửi tới Virtual IP thì router vật lý sẽ trả về địa chỉ MAC ảo này.
# Cơ chế hoạt động
- Các router/server vật lý dùng chung Virtual IP sẽ có 2 trạng thái là MASTER/ACTIVE và BACKUP/SLAVE. Cơ chế failover được xử lý bởi giao thức VRRP
- Khi khởi động lại dịch vụ, toàn bộ các server dùng chung Virtual IP sẽ gia nhập vào một nhóm Multicast. Nhóm này dùng để gửi/nhận các gói tin với độ ưu tiên cao nhất sẽ được chọn làm Master. Khi một nhóm đã có Master thì Master này sẽ chịu trách nhiệm gửi các gói tin quảng bá VRRP định kỳ cho nhóm multicast.
- Nếu có một sự cố gì đó mà các server BACKUP không nhận được các gói tin quảng bá từ Master trong một khoảng thời gian nhất định thì cả nhóm sẽ bầu ra một Master mới tiếp nhận nhiệm vụ của Master cũ
- Khi master cũ hoạt động trở lại bình thường thì router này có thể trở thành Master hoặc Backup tuỳ theo cấu hình độ ưu tiên.
# Cấu hình
## Mô hình thực hiện
![](https://imgur.com/6NDESiS.png)
##
## Các bước cấu hình keepalived
### 1. Cấu hình Reverser Proxy
- Bước 1: Tạo 1 Virtual Host ảo trên Nginx
- Cài kho mở rộng
```
yum install -y epel-release
yum install -y nginx
```
- Khởi động Nginx

Cài đặt

```
yum install gcc kernel-headers kernel-devel
yum install keepalived
```

Cho phép gắn IP ảo lên card mạng và IP Forward

```
echo "net.ipv4.ip_nonlocal_bind = 1" >> /etc/sysctl.conf
echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
sysctl -p
```

Cấu hình trên node master (NGINX 1)

```
cat > /etc/keepalived/keepalived.conf << EOF
vrrp_script chk_nginx {
        script "killall -0 nginx"
        interval 2
        weight 4
}

vrrp_instance VI_1 {
    state MASTER
    interface eth1
    mcast_src_ip 192.168.19.132
    virtual_router_id 50
    priority 100
    advert_int 1
    authentication {
        auth_type AH
        auth_pass 12345
    }
    virtual_ipaddress {
       192.168.19.154/24 dev eth1
    }
    track_script
    {
        chk_nginx
    }
}
EOF
```

Cấu hình trên node backup (NGINX 2)

```
cat > /etc/keepalived/keepalived.conf << EOF
vrrp_script chk_nginx {
        script "killall -0 nginx"     
        interval 2
        weight 4
}
 
vrrp_instance VI_1 {
    state BACKUP
    interface eth1
    mcast_src_ip192.168.19.155
    virtual_router_id 50
    priority 98
    advert_int 1
    authentication {
        auth_type AH
        auth_pass 12345
    }
    virtual_ipaddress {
       192.168.19.154/24 dev eth1
    }
    track_script 
    {
        chk_nginx
    }
}
EOF
```

**Lưu ý**: Thứ tự ưu tiên làm master xác định dựa vào thông số `priority`. Giá trị lớn hơn sẽ được ưu tiên làm master.

Khởi động vào enable keepalived trên cả 2 node

```
systemctl start keepalived
systemctl enable keepalived
```

Nếu ko tắt firewall thì mở firewall để cho phép dải địa chỉ multicast

```
iptables -I INPUT -i eth0 -d 224.0.0.0/8 -p vrrp -j ACCEPT
iptables -I INPUT -i eth0 -d 224.0.0.0/8 -p ah -j ACCEPT
iptables -I OUTPUT -o eth0 -s 224.0.0.0/8 -p vrrp -j ACCEPT
iptables -I OUTPUT -o eth0 -s 224.0.0.0/8 -p ah -j ACCEPT.
service iptables save
```

Kiểm tra ip trên node master (NGINX 1)

Trên NGINX 1

```
[root@nginxsrv ~]# ip a sh eth1
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 52:54:00:75:db:7c brd ff:ff:ff:ff:ff:ff
    inet 192.168.19.132/24 brd 10.10.34.255 scope global noprefixroute eth1
       valid_lft forever preferred_lft forever
    inet192.168.19.154/24 scope global secondary eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::33fa:f515:4bae:85a6/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

Tăt thử note master ta sẽ thấy VIP đã được chuyển cho node backup (NGINX 2)

```
[root@localhost ~]# ip a sh eth1
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 52:54:00:ca:ef:b8 brd ff:ff:ff:ff:ff:ff
    inet192.168.19.155/24 brd 10.10.34.255 scope global noprefixroute eth1
       valid_lft forever preferred_lft forever
    inet192.168.19.154/24 scope global secondary eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:feca:efb8/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```