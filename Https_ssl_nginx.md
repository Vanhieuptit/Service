# Cấu hình https sử dụng let's encrypt
## Cài đặt certbot
```
yum install epel-release -y
yum install certbot-nginx -y
```
## Sinh ssl bằng let's encrypt cho site test.1lab.xyz
```
certbot --nginx -d test.niemdt.com
```
![](https://imgur.com/kwXo5WE.png)
- Kiểm tra file **/etc/nginx/sites-available/test.1lab.xyz** lúc này sẽ có thêm 1 số dòng cấu hình như sau
![](https://imgur.com/HUoFXi3.png)
## Cấu hình tự động renew Certification
- Vì Certification sẽ bị hết hạn trong vòng 3 tháng nên ta có thể sử dụng crontab để tự động renew Certification
```
yum install wget -y
wget https://dl.eff.org/certbot-auto && chmod a+x certbot-auto
mv certbot-auto /etc/letsencrypt/
echo "0 2 * * 1 cd /etc/letsencrypt/ && ./certbot-auto renew && systemctl restart nginx" >> /etc/crontab
systemctl restart crond
```
- Như vậy Certification sẽ được làm mới vào 2:00 thứ 2 hàng tuần