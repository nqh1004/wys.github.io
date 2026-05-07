## The Existed File

The system will check whether the file exists or not. We have also implemented blocklist keywords to detect hackers.

## Tổng quan

Những thứ cần thiết để giải được challenge này ta cần có kiến thức về code và lỗi Command Injection,các tools sử dụng : Burp,Webhook
<img width="1105" height="492" alt="image" src="https://github.com/user-attachments/assets/f94ce030-dab1-4d1b-b84b-e1e01bca69db" />

## Phân tích bài

- Truy cập vào bài thì ta thấy web trả về xem liệu đường dẫn có tồn tại hay không, nếu có sẽ hiện
    
    như ảnh bên dưới

  <img width="605" height="319" alt="image" src="https://github.com/user-attachments/assets/a3c5e8e7-8827-4d26-99a4-2e5a3d887cd6" />

- Ở đây là mã nguồn mở nên ta down về để xem source code bên trong có gì
- Chúng ta có thể thấy ở đây có blacklisted để filter các dữ liệu nhập vào

  <img width="1541" height="942" alt="image" src="https://github.com/user-attachments/assets/b3631107-a698-44c5-a804-db0eaaad7d1a" />

- Ở đoạn code bên dưới thì ta thấy dữ liệu nhập vào sẽ bị filter khoảng trắng
- Dòng code bên dưới sẽ loại bỏ tất cả khoảng trắng trong biến file_path

 ```bash
file_path = file_path.translate({ord(c): None for c in string.whitespace})
```
<img width="872" height="194" alt="image" src="https://github.com/user-attachments/assets/72840393-820b-4b63-98b0-81fbf21336a5" />

Dòng code bên dưới cho phép thực hiện lệnh trực tiếp từ người dùng. Điều này tạo ra lổ hỏng Command Injection


