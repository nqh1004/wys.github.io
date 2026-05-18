
<img width="758" height="271" alt="image" src="https://github.com/user-attachments/assets/c2af011a-4b6f-4cc3-a829-b8202b4ff1d7" />


Flag 1 — Stored XSS + CSRF tạo user admin
Phân tích

Không có trang register public
Endpoint /newUser không có CSRF token bảo vệ
Admin bot đọc ticket và click link bên trong
/newUser chấp nhận GET request với params

payload : 
```html
<a href="http://localhost/newUser?username=hacker&password=hacker123&password2=hacker123">click here</a>
```
<img width="657" height="347" alt="image" src="https://github.com/user-attachments/assets/87585e8c-ae97-426e-b842-043266ba160c" />

Tại sao dùng localhost?

Admin bot chạy bên trong server → phải dùng localhost thay vì domain ngoài

Sau khi bot click vào thì sẽ tạo tài khoản theo username đã tạo 
Login vào tài khoản

<img width="644" height="326" alt="image" src="https://github.com/user-attachments/assets/12c3766c-4313-463e-8241-ca8727f26ad3" />


<img width="772" height="423" alt="image" src="https://github.com/user-attachments/assets/3e503601-78ee-49e9-b1fc-4a300c572cff" />


<img width="1109" height="319" alt="image" src="https://github.com/user-attachments/assets/6052f532-0f69-4b79-b98b-5aba532e9bbb" />
