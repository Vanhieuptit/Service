- Dưới đây là chi tiết các lệnh thường dùng để gán cho user trong MySQL
    - ALL PRIVILEGES: cho phép MySQL user thực hiện toàn quyền trên databases (hoặc 1 vài db được thiết lập)
    - CREATE: cho phép user tạo mới tables hoặc databases
    - DROP: cho phép xoá tables hoặc databases
    - DELETE: cho phép xoá bản ghi dữ liệu trong bảng tables
    - INSERT: cho phép thêm bản ghi mới vào bảng csdl
    - SELECT: cho phép sử dụng lệnh Select để tìm kiếm dữ liệu 
    - UPDATE- Cho phép cập nhật csdl
    - GRANT OPTION: cho phép gán hoặc xoá quyền của người dùng khác.
## Cú pháp gán quyền cho user bằng lệnh đơn
```
GRANT loai_cap_phep ON ten_database.ten_bang TO 'username'@'localhost';
```
- Nếu muốn cho phép user truy cập tất cả database hoặc tất cả bảng, hãy dùng dấu * thay cho tên database hoặc table. Ví dụ: `GRANT ALL PRIVILEGES ON * . * TO 'newuser'@'localhost';`
- Có thể gán nhiều quyền theo ý của mình bằng cách thêm dấu `,` vào sau các quyền. Ví dụ `GRANT CREATE, SELECT ON database_name.table_name TO 'username'@'localhost';`. Ở câu lệnh trên có 2 quyền được cho vào là CREATE và SELECT.
- Thu hồi lại quyền của user, dùng lệnh REVOKE: `REVOKE type_of_permission ON database_name.table_name FROM 'username'@'localhost';`
- Xoá user bằng lệnh `DROP USER 'username'@'localhost';`
- Kiểm tra user mới tạo thành công chưa và có thể đăng nhập được hay chưa bằng lệnh `mysql -u[username] -p[password]`. Ví dụ `mysql -u root -p 12345`. là đăng nhập với user root mật khẩu 12345.
- Hiện thị quyền của user bằng lệnh `SHOW GRANTS FOR 'username'@'localhost';`




- 
