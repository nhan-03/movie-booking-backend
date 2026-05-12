# Database Design - Movie Booking System

Tài liệu mô tả chi tiết cấu trúc các bảng và mối quan hệ trong cơ sở dữ liệu của hệ thống đặt vé xem phim.

---

## 1. Entity Relationship Diagram (ERD)

![ER Diagram](/docs/images/erd.png)

---

## 2. Database Schema

### 2.1. Table: `users`
Quản lý thông tin tài khoản và phân quyền.

| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| id | BIGINT | PK, Auto Inc | Mã định danh |
| full_name | VARCHAR(100) | | Họ và tên |
| email | VARCHAR(255) | UNIQUE, Not Null | Email đăng nhập |
| password | VARCHAR(255) | Not Null | Mật khẩu đã mã hóa |
| phone_number | VARCHAR(20) | | Số điện thoại |
| role | VARCHAR(20) | | USER / ADMIN |
| is_active | BOOLEAN | | Trạng thái tài khoản |
| created_at | DATETIME | | Thời điểm tạo |
| updated_at | DATETIME | | Thời điểm cập nhật |

### 2.2. Table: `movies`
Danh sách các phim trong hệ thống.

| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| id | BIGINT | PK, Auto Inc | Mã phim |
| title | VARCHAR(255) | Not Null | Tên phim |
| description | TEXT | | Nội dung phim |
| duration | INT | | Thời lượng (phút) |
| language | VARCHAR(50) | | Ngôn ngữ |
| release_date | DATE | | Ngày khởi chiếu |
| poster_url | VARCHAR(500) | | Link ảnh poster |
| trailer_url | VARCHAR(500) | | Link trailer |
| age_rating | VARCHAR(10) | | Phân loại độ tuổi |
| status | VARCHAR(20) | | Trạng thái (Đang chiếu, Sắp chiếu) |

### 2.3. Table: `cinemas`
Hệ thống rạp chiếu.

| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| id | BIGINT | PK, Auto Inc | Mã rạp |
| name | VARCHAR(255) | Not Null | Tên rạp |
| address | VARCHAR(255) | | Địa chỉ |
| city | VARCHAR(100) | | Thành phố |

### 2.4. Table: `rooms`
Phòng chiếu thuộc về một rạp cụ thể.

| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| id | BIGINT | PK, Auto Inc | Mã phòng |
| cinema_id | BIGINT | FK | Thuộc rạp nào |
| name | VARCHAR(50) | | Tên phòng |
| total_seats | INT | | Số lượng ghế |

### 2.5. Table: `seats`
Vị trí ghế trong phòng chiếu.

| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| id | BIGINT | PK, Auto Inc | Mã ghế |
| room_id | BIGINT | FK | Thuộc phòng nào |
| seat_row | VARCHAR(5) | | Hàng ghế (A, B...) |
| seat_number | INT | | Số thứ tự ghế |
| seat_type | VARCHAR(20) | | NORMAL / VIP / COUPLE |

### 2.6. Table: `showtimes`
Lịch chiếu phim cụ thể.

| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| id | BIGINT | PK, Auto Inc | Mã suất chiếu |
| movie_id | BIGINT | FK | Phim nào |
| room_id | BIGINT | FK | Phòng nào |
| start_time | DATETIME | | Giờ bắt đầu |
| end_time | DATETIME | | Giờ kết thúc |
| price | DECIMAL(10,2) | | Giá vé |
| status | VARCHAR(20) | | Trạng thái suất chiếu |

### 2.7. Table: `bookings`
Đơn đặt vé của người dùng.

| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| id | BIGINT | PK, Auto Inc | Mã đơn đặt vé |
| user_id | BIGINT | FK | Người đặt |
| showtime_id | BIGINT | FK | Suất chiếu nào |
| booking_code | VARCHAR(50) | UNIQUE | Mã nhận vé |
| total_amount | DECIMAL(10,2) | | Tổng tiền |
| booking_status | VARCHAR(20) | | SUCCESS / CANCELLED |
| payment_status | VARCHAR(20) | | PAID / UNPAID |

### 2.8. Table: `booking_seats`
Bảng trung gian lưu vết các ghế được chọn trong một đơn hàng.

| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| id | BIGINT | PK, Auto Inc | |
| booking_id | BIGINT | FK | Thuộc đơn nào |
| seat_id | BIGINT | FK | Ghế nào |

### 2.9. Table: `payments`
Chi tiết giao dịch thanh toán.

| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| id | BIGINT | PK, Auto Inc | Mã giao dịch |
| booking_id | BIGINT | FK | Đơn đặt vé nào |
| payment_method | VARCHAR(50) | | Ví dụ: MoMo, VNPay |
| amount | DECIMAL(10,2) | | Số tiền thanh toán |
| transaction_id | VARCHAR(255) | | Mã từ cổng thanh toán |
| payment_status | VARCHAR(20) | | Trạng thái giao dịch |
| paid_at | DATETIME | | Thời điểm thanh toán |

---

## 3. Relationships

Dưới đây là mô tả các liên kết chính giữa các thực thể trong hệ thống:

*   **One-to-Many (1:N):**
    *   `cinemas` & `rooms`: Một rạp có nhiều phòng chiếu.
    *   `rooms` & `seats`: Một phòng chiếu chứa nhiều ghế.
    *   `movies` & `showtimes`: Một bộ phim có thể được chiếu ở nhiều khung giờ khác nhau.
    *   `rooms` & `showtimes`: Một phòng có thể tổ chức nhiều suất chiếu (tại các thời điểm khác nhau).
    *   `users` & `bookings`: Một người dùng có thể thực hiện nhiều đơn đặt vé.
    *   `bookings` & `payments`: Một đơn hàng có thể có nhiều bản ghi thanh toán (trường hợp thanh toán lỗi và thực hiện lại).

*   **Many-to-Many (N:N):**
    *   `bookings` & `seats`: Một đơn đặt vé có thể đặt nhiều ghế và một ghế có thể xuất hiện trong nhiều đơn đặt vé (ở các suất chiếu khác nhau). Mối quan hệ này được giải quyết thông qua bảng trung gian **`booking_seats`**.

---

## 4. Optimization (Indexes)

Các chỉ mục (Index) được thiết lập để tối ưu hóa hiệu suất cho các truy vấn phổ biến:

*   **`idx_movies_title`**: Đánh trên cột `title` của bảng `movies`. Giúp tìm kiếm phim theo tên nhanh hơn khi người dùng gõ từ khóa.
*   **`idx_showtimes_start_time`**: Đánh trên cột `start_time` của bảng `showtimes`. Hỗ trợ việc lọc và sắp xếp lịch chiếu theo thời gian nhanh chóng.
*   **`idx_users_email`**: Đánh trên cột `email` của bảng `users`. Tăng tốc độ kiểm tra tài khoản khi người dùng đăng nhập.
*   **`idx_bookings_code`**: Đánh trên cột `booking_code` của bảng `bookings`. Giúp nhân viên rạp tìm kiếm thông tin vé để xuất cho khách hàng tức thì.
*   **`idx_payments_transaction`**: Đánh trên cột `transaction_id` của bảng `payments` để đối soát nhanh với các cổng thanh toán (VNPay, MoMo).