# Report 1 page - Lab 6 AES-CBC Socket

## Thông tin nhóm

- Thành viên 1: Phạm Minh Duy - 1871020192
- Thành viên 2: Vũ Tuấn Minh - 1871020394

## Mục tiêu

Bài lab này nhằm triển khai hệ thống gửi và nhận dữ liệu được mã hóa bằng AES-CBC qua TCP socket. 
Hệ thống tách biệt kênh khóa (key channel) để trao đổi AES key và IV, và kênh dữ liệu (data channel) để truyền ciphertext.
Sinh viên sẽ học cách cài đặt AES-CBC với PKCS#7 padding, thiết kế header độ dài cho truyền dữ liệu an toàn, 
viết test cho các tình huống đúng và sai, và phân tích điểm yếu bảo mật của việc gửi key/IV plaintext.

## Phân công thực hiện

- Phạm Minh Duy (1871020192): Phụ trách triển khai sender.py, kênh khóa, log gửi.
- Vũ Tuấn Minh (1871020394): Phụ trách triển khai receiver.py, kênh dữ liệu, giải mã.
- Chung: aes_socket_utils.py, tests, report, threat model.

## Cách làm

Sender đọc plaintext từ file, env hoặc input, tạo AES key và IV ngẫu nhiên, mã hóa với AES-CBC và PKCS#7 padding, 
gửi key/IV qua kênh khóa (KEY_PORT) với format [key_length:4][key][iv], 
sau đó gửi ciphertext qua kênh dữ liệu (DATA_PORT) với format [ciphertext_length:4][ciphertext].
Receiver lắng nghe kênh khóa để nhận key/IV, sau đó kênh dữ liệu để nhận ciphertext, 
giải mã và bỏ padding để lấy plaintext gốc. 
Sử dụng length header để đảm bảo nhận đủ dữ liệu qua TCP stream.

## Kết quả

Hệ thống chạy thành công với sender gửi "Xin chao FIT4012" và receiver nhận đúng plaintext. 
Log ghi đầy đủ key, IV, ciphertext hex. 
Tests pass bao gồm roundtrip AES, packet build/parse, local integration, negative cases như wrong key, tamper, padding error. 
Output mẫu: Plaintext "FIT4012 Lab 6", ciphertext hex, giải mã thành công.

## Kết luận

Bài học kỹ thuật: Tách kênh giúp quản lý bảo mật tốt hơn, length header đảm bảo truyền dữ liệu hoàn chỉnh qua TCP. 
Bài học bảo mật: Gửi key plaintext là lỗ hổng lớn, cần cơ chế trao đổi khóa an toàn như TLS/DH; 
AES-CBC cần IV unique để tránh attacks; cần authentication và integrity checks.
