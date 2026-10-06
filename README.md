Báo Cáo Thực Hành: Cấu Hình Tường Lửa UFW Và Chẩn Đoán Cổng Mạng

Môn học / Session: Session 06

Bài tập: Bài 3 - Cấu hình tường lửa UFW và chuẩn đoán cổng mạng

Đường dẫn nộp bài: homework/session_06/ex3/

1. Mục tiêu

Sử dụng công cụ tường lửa UFW (Uncomplicated Firewall) để bảo vệ máy chủ Cloud VPS.

Thiết lập quy tắc chặn/mở cổng mạng tối thiểu phục vụ deploy ứng dụng web an toàn.

Sử dụng các lệnh chẩn đoán hệ thống mạng (ss, curl, ufw status) để kiểm tra cổng kết nối.

2. Các bước thực hiện

Bước 1: Thiết lập chính sách mặc định (Default Policies)

Thiết lập chặn toàn bộ kết nối đi vào và cho phép toàn bộ kết nối đi ra:

sudo ufw default deny incoming
sudo ufw default allow outgoing


Bước 2: Cấu hình mở cổng cần thiết

Mở cổng kết nối quản trị SSH và cổng ứng dụng Web:

# Mở cổng SSH (Port 22/tcp) để tránh mất quyền điều khiển VPS
sudo ufw allow 22/tcp

# Mở cổng ứng dụng Web (Port 8080/tcp)
sudo ufw allow 8080/tcp


Bước 3: Kích hoạt tường lửa UFW

Bật tường lửa và xác nhận kích hoạt (y):

sudo ufw enable


3. Kết quả kiểm tra & Chẩn đoán

3.1. Kiểm tra trạng thái tường lửa (sudo ufw status verbose)

Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere                  
8080/tcp                   ALLOW IN    Anywhere                  
22/tcp (v6)                ALLOW IN    Anywhere (v6)             
8080/tcp (v6)              ALLOW IN    Anywhere (v6)             


Nhận xét:

Tường lửa đang ở trạng thái active.

Chính sách mặc định: deny (incoming) và allow (outgoing).

Các cổng mở: Cổng 22/tcp và 8080/tcp từ mọi nguồn (Anywhere).

3.2. Kiểm tra các cổng đang lắng nghe (ss -tlnp)

Lệnh kiểm tra:

sudo ss -tlnp


Kết quả mẫu:

State    Recv-Q   Send-Q     Local Address:Port      Peer Address:Port   Process                                          
LISTEN   0        128              0.0.0.0:22             0.0.0.0:*       users:(("sshd",pid=789,fd=3))                   
LISTEN   0        511              0.0.0.0:8080           0.0.0.0:*       users:(("node",pid=1420,fd=18))                  
LISTEN   0        128                 [::]:22                [::]:*       users:(("sshd",pid=789,fd=4))                   
LISTEN   0        511                 [::]:8080              [::]:*       users:(("node",pid=1420,fd=19))                  


3.3. Kiểm tra kết nối cục bộ (curl)

Kiểm tra khả năng phản hồi của ứng dụng tại cổng 8080:

curl -I http://localhost:8080


Kết quả phản hồi mẫu:

HTTP/1.1 200 OK
Date: Tue, 06 Oct 2026 13:40:00 GMT
Content-Type: text/html; charset=UTF-8
Connection: keep-alive


4. Kết luận

Hệ thống VPS đã được bảo vệ thành công bởi UFW, giảm thiểu nguy cơ quét cổng và tấn công trái phép từ bên ngoài.

Ứng dụng web tại cổng 8080 sẵn sàng phục vụ lưu lượng truy cập mà vẫn đảm bảo an toàn cho máy chủ.