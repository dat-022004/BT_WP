# Bài Tập: Triển Khai Website WordPress Bằng Docker và Cloudflare Tunnel

Dự án này là bài tập triển khai một website sử dụng mã nguồn mở WordPress thông qua Docker Compose trên hệ điều hành Ubuntu, và công khai web ra Internet bằng Cloudflare Tunnel.

---

## 1. Cấu hình Docker Compose

Hệ thống được cấu hình và khởi chạy với 3 service chính:
- **MariaDB** (`mariadb:latest`): Hệ quản trị cơ sở dữ liệu.
- **PhpMyAdmin** (`phpmyadmin:latest`): Công cụ giao diện web quản trị MariaDB.
- **WordPress** (`wordpress:latest`): Mã nguồn mở WordPress.

Sau khi thiết lập file `docker-compose.yml`, tiến hành chạy lệnh `docker compose up -d`. Dưới đây là kết quả kiểm tra các container đang chạy bằng lệnh `docker ps`:

![Docker Containers Running](img/z7814507093322_429a420b0911194e876a8008a5d29a32.jpg)

Truy cập vào PhpMyAdmin và đăng nhập với quyền root để tạo một cơ sở dữ liệu trống có tên là `wordpress_db`. Cơ sở dữ liệu này sẽ được WordPress sử dụng để tự động khởi tạo các bảng:

![Giao diện PhpMyAdmin](img/z7814498089032_4bc773878d4e987346a59724fbd0c01a.jpg)

---

## 2. Quản Trị Và Đăng Bài Trên WordPress (Localhost)

Trước khi public website ra Internet, tôi đã tiến hành cài đặt WordPress và tạo các bài viết ở môi trường Localhost. Dưới đây là giao diện Admin Dashboard trực quan của WordPress khi truy cập qua localhost:

![Giao diện Admin WordPress Local](img/z7814471106876_168a9dc77c193c20f25b008d04b7b2f3.jpg)

Tiếp theo, tôi đã tiến hành tạo các bài viết theo yêu cầu của bài tập:
1. **Bài viết giới thiệu bản thân**: Có chứa thông tin cá nhân, hình ảnh và các sở thích.
2. **Bài viết giới thiệu về ngành học**: Bài viết về ngành Kỹ thuật phần mềm tại trường Đại học Kỹ thuật Công nghiệp (TNUT).

Hình ảnh dưới đây minh họa quá trình soạn thảo bài "Giới thiệu bản thân" thông qua localhost. Trình soạn thảo Gutenberg của WordPress cho phép dễ dàng chèn hình ảnh, căn chỉnh văn bản một cách trực quan:

![Giao diện soạn thảo bài viết Local](img/z7814489315484_2268e566eaea9cbebe51ea8c719c73d9.jpg)

---

## 3. Public Website Bằng Cloudflare Tunnel

Sau khi website đã có đầy đủ nội dung, để public website ra ngoài Internet với một tên miền, tôi đã cấu hình sử dụng Cloudflare Tunnel (`cloudflared`).

Dưới đây là màn hình Terminal cho thấy tiến trình Cloudflare Tunnel đang chạy và duy trì kết nối (Proxy) thành công:

![Cloudflared Tunnel Logs](img/z7814564449829_397e09b69a93196ddfce8b81b34ac295.jpg)

Giao diện quản lý DNS trên Cloudflare xác nhận bản ghi (Record) đã được trỏ thành công về Tunnel của WordPress, đảm bảo người dùng có thể truy cập website qua sub-domain an toàn:

![Cloudflare DNS Records](img/z7814572909375_a8be7ebe77ac2e5701c80b0537f4674b.jpg)

**Kết Quả Hoạt Động:** 
Dưới đây là màn hình quản lý danh sách các bài viết đã được xuất bản trên website. **Lưu ý trên thanh địa chỉ, website lúc này đang được truy cập và quản trị thành công thông qua tên miền chính thức (`wp.dangdinhdat.id.vn`) thay vì localhost, chứng minh quá trình cấu hình Cloudflare Tunnel đã thành công tuyệt đối:**

![Danh sách bài viết qua Domain](img/z7814570208540_9de5afad4a6b2b8396ad82491987f3c9.jpg)

---

## 4. Nhận Xét Việc Sử Dụng Mã Nguồn Mở WordPress

Sau quá trình cài đặt và làm quen với website WordPress, tôi có những đánh giá sau:

- **Tốn công sức thế nào:** Việc sử dụng Docker kết hợp Docker Compose giúp cho việc triển khai ban đầu (Setup) trở nên cực kỳ tiết kiệm thời gian và công sức. Thay vì phải cài đặt thủ công từng thành phần như PHP, Apache/Nginx, MySQL trên Ubuntu, chỉ cần 1 file YAML là hệ thống đã hoạt động. Tuy nhiên, việc thiết lập Cloudflare Tunnel yêu cầu người thực hiện có một chút kiến thức về dòng lệnh và quản trị DNS.
- **Độ dễ/khó sử dụng:** WordPress **rất dễ sử dụng**. Giao diện Dashboard được thiết kế khoa học, hỗ trợ tiếng Việt giúp người mới dễ dàng thao tác. Trình soạn thảo bài viết (Gutenberg) cho phép thêm hình ảnh, video, âm thanh linh hoạt thông qua dạng Block (khối), gần giống như đang thao tác trên Microsoft Word hay Notion mà không yêu cầu kiến thức lập trình HTML/CSS.
- **Tốn kém tài nguyên máy chủ:** 
  - **RAM:** WordPress chạy cùng MariaDB qua Docker tốn một lượng RAM nhất định. Hệ thống cần tối thiểu khoảng **1GB đến 2GB RAM** để vận hành trơn tru. Quá trình xử lý PHP và Query MySQL có thể dẫn đến hiện tượng thiếu RAM nếu chạy trên các VPS cấu hình quá thấp (như 512MB).
  - **CPU:** Khi chỉ có các tác vụ đọc bài viết thông thường, mức sử dụng CPU là không đáng kể. Tuy nhiên, khi tải lên hình ảnh/video có dung lượng lớn hoặc khi hệ thống xử lý nén ảnh (Thumbnail generation), CPU sẽ tăng nhẹ trong thời gian ngắn. Do đó, 1 vCPU là khá đủ cho website cá nhân, nhưng nếu lượng truy cập lớn sẽ cần nâng cấp thêm tài nguyên.
