# Report 1 page - Lab 3

## Thông tin nhóm
- Thành viên 1: Lưu Phương Thảo 
- Thành viên 2: Nguyễn Anh Đức

## Mục tiêu
- Xây dựng thành công mô hình Client-Server giao tiếp qua TCP Socket.
- Hiểu và cài đặt được thuật toán mã hoá đối xứng DES ở chế độ CBC, kết hợp kỹ thuật đệm PKCS#7.
- Nắm vững quy trình đóng gói dữ liệu truyền tải trên mạng theo cấu trúc tuần tự: `Key (8 bytes)` + `IV (8 bytes)` + `Header báo độ dài (4 bytes)` + `Ciphertext`.
- Nhận thức được các lỗ hổng bảo mật nghiêm trọng trong mô hình gửi trực tiếp khoá bí mật qua đường truyền chưa được mã hoá.

## Phân công thực hiện
Nhóm gồm 2 thành viên chia công việc theo 2 luồng xử lý chính của hệ thống:
- **Nguyễn Anh Đức (Sender):** Đóng vai trò khởi tạo kết nối. Lập trình file `sender.py` và các hàm xử lý cốt lõi trong `des_socket_utils.py` (bao gồm hàm tạo padding, hàm mã hoá DES-CBC và hàm đóng gói packet). Chịu trách nhiệm tổng hợp file `README.md` và `report-1page.md`.
- **[Điền tên bạn cùng nhóm] (Receiver):** Đóng vai trò máy chủ lắng nghe. Lập trình file `receiver.py` thực hiện bóc tách gói tin, giải mã, gỡ padding và bắt các ngoại lệ (Exception) khi mạng lỗi hoặc dữ liệu bị can thiệp. Chịu trách nhiệm viết phân tích rủi ro `threat-model-1page.md`.
- **Phần làm chung:** Cùng thực hiện chạy 5 ca kiểm thử (Test cases), trích xuất file log thực tế và thực hiện đánh giá chéo (Peer-review).

## Cách làm
- **Xây dựng bộ công cụ mật mã (`des_socket_utils.py`):** - Cài đặt hàm đệm PKCS#7 để đảm bảo độ dài bản rõ luôn chia hết cho 8 byte (Block size của DES).
  - Sử dụng thư viện `pycryptodome` để mã hoá/giải mã DES ở chế độ CBC. Key và IV (mỗi loại 8 byte) được sinh ngẫu nhiên cho mỗi phiên giao dịch.
  - Xây dựng hàm đóng gói (Build Packet) ghép nối dữ liệu theo chuẩn: `8 byte Key` + `8 byte IV` + `4 byte Header độ dài` (sử dụng `struct.pack` định dạng Big-endian) + `Ciphertext`.
- **Triển khai người gửi (`sender.py`):** Khởi tạo kết nối TCP Socket tới Server. Đọc thông điệp truyền vào, gọi hàm mã hoá và đóng gói gói tin, sau đó đẩy toàn bộ byte dữ liệu qua đường truyền mạng.
- **Triển khai người nhận (`receiver.py`):** Khởi tạo TCP Server lắng nghe kết nối. Khi có kết nối, tiến hành đọc chính xác 20 byte đầu tiên để bóc tách Key, IV và Độ dài bản mã. Tiếp tục đọc Ciphertext dựa trên độ dài thu được, giải mã và gỡ padding để khôi phục bản rõ.
- **Kiểm thử:** Chạy song song Sender và Receiver trên 2 Terminal (Localhost). Thực hiện gửi tin nhắn chuẩn để lấy log thành công, đồng thời can thiệp mã nguồn để thử nghiệm các trường hợp lỗi (Negative tests) như sai padding, gửi thiếu độ dài header hoặc đứt mạng giữa chừng để xác nhận Receiver bắt lỗi tốt.

## Kết quả
Hệ thống đã hoạt động trơn tru và đáp ứng đầy đủ các yêu cầu kiểm thử:
- **Test ca 1 (Happy path):** Sender đã sinh khoá thành công, mã hoá bản tin và gửi qua Socket. Receiver nhận đúng luồng byte, giải mã và in ra chính xác bản rõ "Xin chao FIT4012" mà không bị lỗi padding.
- **Các ca kiểm thử ngoại lệ (Negative tests):** Khi nhóm cố tình can thiệp vào mã nguồn Sender để tạo lỗi (như gửi sai độ dài Header, cắt đứt kết nối đột ngột không gửi Ciphertext, hoặc sửa đổi bit của Ciphertext/Key), hệ thống Receiver đều phát hiện và báo lỗi hợp lý (như lỗi ConnectionReset, lỗi CryptographicException do sai padding).
- Toàn bộ log minh chứng của quá trình chạy thật đã được lưu đầy đủ vào thư mục `logs/`.

## Kết luận
Qua bài Lab 3, nhóm đã củng cố được kỹ năng lập trình mạng (Socket) và cách tích hợp thư viện mật mã học vào thực tế. Hệ thống hiện tại đã mô phỏng thành công luồng truyền tải dữ liệu mã hoá. Tuy nhiên, về mặt thiết kế bảo mật, đây là một hệ thống mang tính chất học thuật. Việc gửi kèm Key và IV dưới dạng bản rõ (plaintext) cùng với dữ liệu trên một đường truyền TCP chưa được bảo vệ là một lỗ hổng chí mạng (kẻ tấn công có thể bắt gói tin và giải mã dễ dàng). Trong thực tế, hệ thống cần được nâng cấp lên các tiêu chuẩn an toàn hơn như sử dụng trao đổi khoá thuật toán phi đối xứng (RSA/Diffie-Hellman) hoặc chạy trên nền giao thức TLS/SSL.
