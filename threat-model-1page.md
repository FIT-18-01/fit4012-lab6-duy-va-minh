# Threat Model - Lab 6 AES-CBC Socket

## Thông tin nhóm

- Thành viên 1: Phạm Minh Duy - 1871020192
- Thành viên 2: Vũ Tuấn Minh - 1871020394

## Assets

- Plaintext: Dữ liệu gốc cần bảo vệ khỏi lộ.
- AES key và IV: Khóa mã hóa, nếu lộ sẽ cho phép giải mã ciphertext.
- Ciphertext: Dữ liệu mã hóa đang truyền, cần bảo vệ khỏi sửa đổi.
- File đầu vào/đầu ra: Plaintext trong file.
- Logs: Chứa key/IV/ciphertext, nếu lộ sẽ rò rỉ thông tin nhạy cảm.

## Attacker model

Attacker có khả năng nghe lén mạng LAN, bắt gói tin TCP, sửa đổi ciphertext trong transit, 
gửi lại packet cũ (replay), hoặc đọc logs nếu có quyền truy cập file hệ thống. 
Giả định attacker không có quyền truy cập vật lý vào máy sender/receiver.

## Threats

- Key disclosure: Key/IV gửi plaintext qua kênh khóa, attacker nghe lén có thể giải mã ciphertext.
- Tampering: Ciphertext bị sửa trong transit, dẫn đến giải mã sai hoặc lỗi.
- Replay attack: Attacker gửi lại packet cũ, receiver xử lý lại.
- Log leakage: Key/IV ghi vào log file, nếu attacker đọc được sẽ lộ khóa.
- No authentication: Receiver không xác thực sender, bất kỳ ai cũng có thể gửi packet.

## Mitigations

- Không gửi key plaintext: Dùng TLS hoặc Diffie-Hellman để trao đổi khóa an toàn.
- Dùng AES-GCM: Thay CBC để có xác thực dữ liệu tích hợp.
- Không ghi key vào log: Chỉ log trong môi trường dev, mã hóa log nếu cần.
- Chống replay: Thêm nonce hoặc timestamp vào packet.
- Xác thực sender: Dùng certificate hoặc shared secret để verify.

## Residual risks

Hệ thống vẫn không an toàn cho production vì kênh khóa chỉ mô phỏng gửi plaintext, chưa có TLS, 
chưa có authentication, chưa chống replay đầy đủ, và logs có thể lộ key nếu không bảo vệ.
