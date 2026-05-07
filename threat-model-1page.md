# Threat Model - Lab 3

## Thông tin nhóm
- Thành viên 1: Lưu Phương Thảo
- Thành viên 2: Nguyễn Anh Đức

## Assets
- Bản rõ (Plaintext), Khóa DES (8 byte) và Vector khởi tạo IV (8 byte).

## Attacker model
- Kẻ tấn công nằm trong mạng nội bộ, có khả năng nghe lén (Sniffing) và sửa đổi gói tin (Tampering).

## Threats
- **Lộ khóa:** Do Key/IV gửi dạng plaintext ngay đầu gói tin nên dễ bị đánh cắp.
- **Sửa đổi dữ liệu:** DES-CBC không xác thực nên kẻ tấn công có thể thay đổi bản mã mà Receiver không biết.
- **Tấn công DoS:** Gửi giá trị Length cực lớn làm tràn bộ nhớ Receiver.

## Mitigations
- Sử dụng TLS/SSL hoặc mã hóa Key bằng RSA. Thêm HMAC để xác thực thông điệp. Giới hạn kích thước gói tin nhận vào.

## Residual risks
- Thuật toán DES 56-bit đã lỗi thời, vẫn có nguy cơ bị Brute-force. Cần nâng cấp lên AES.