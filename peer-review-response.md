# Peer Review Response

## Thông tin nhóm
- Thành viên 1: Lưu Phương Thảo
- Thành viên 2: Nguyễn Anh Đức

## Thành viên 1 góp ý cho thành viên 2
Code Receiver bắt lỗi tốt, đọc gói tin chính xác. Tuy nhiên có thể bổ sung thêm log thân thiện hơn khi Receiver bị ngắt kết nối đột ngột.

## Thành viên 2 góp ý cho thành viên 1
Code Sender chạy mượt, chia module gọn gàng. Góp ý nên tách mã Hex bằng dấu cách để dễ đọc hơn trên terminal.

## Nhóm đã sửa gì sau góp ý
1. Đã cập nhật lại định dạng in log mã Hex bên Sender cho dễ nhìn.
2. Cập nhật các khối try-except ở bên Receiver để bắt lỗi Timeout.
3. Đã chạy lại test và xuất log thành công.
