# **CẢNH BÁO LỖ HỔNG**

Ngày 29 tháng 09, 2024

**Mô tả:** 

Báo cáo này mô tả chi tiết quá trình và kết quả kiểm thử ứng dụng Memejutsu được thực hiện bởi Quoc Huy trong tháng 09,2024.

**Đối tượng** : Trang Web Memejutsu

- Ứng dụng: [http://memejutsu-68f6f11c4a.cyberjutsu-lab.tech/](http://memejutsu-68f6f11c4a.cyberjutsu-lab.tech/random)
- Server : http://images.memejutsu-68f6f11c4a.cyberjutsu-lab.tech/

**Thành viên** : Quoc Huy

**Công cụ :** Burp Suite,VS Code

### **Mục lục**

1. **Tổng quan**
2. **Phạm vi**
3. **Lỗ hổng**
    
    **MJ-01-001: Infomation disclosure ability to leaked all source code**
    
    **MJ-01-002: Insecure Direct Object Reference (IDOR) Personal information Disclosure**
    
    **MJ-01-003:SQL Injection (SQLI) leading to read all database**
    
    **MJ-01-004:Path Traversal Due to Insecure Cookie Serialization**
    
    **MJ-01-005: Blind OS Command Injection** 
    
4. **Kết luận**

### 1. Tổng quan

- Memejutsu là ứng dụng website có tính năng upload file ảnh lên cộng đồng , họ có thể thay đổi ảnh đại diện và chọn các tính năng upload ảnh random
- Bản báo cáo này liệt kê các lỗ hỏng bảo mật và những vấn đề liên quan được tìm thấy trong quá trình kiểm thử ứng dụng Memejutsu trên máy tính. Quá trình kiểm thử được thực hiện dưới hình thức whitebox testing

|  | Nghiêm Trọng
Critical | Cao
High | Trung Bình
Medium | Thấp
Low | Không
None |
| --- | --- | --- | --- | --- | --- |
| Memejutsu | 1 | 2 | 2 |  |  |
|  |  |  |  |  |  |

### 2. Phạm vi

|  | Môi trường | Phiên bản | Special privilege | Soucre code |
| --- | --- | --- | --- | --- |
| Memejutsu | Windown 
MacOS |  | không | có |

### 3.Lỗ hổng

### **MJ-01-001: Infomation disclosure ability to leaked all source code [Medium]**

**Description and impact**

-File `robots.txt` của mã nguồn được lưu trữ trên server: [`http://memejutsu-68f6f11c4a.cyberjutsu-lab.tech/`](http://memejutsu-68f6f11c4a.cyberjutsu-lab.tech/random) dẫn đến địa chỉ của file backup trên server, kẻ tấn công có thể truy cập vào file `robots.txt` để có thể đọc được nội dung mã nguồn ứng dụng.

**Step to reproduce**

- Kiểm tra đường dẫn `robots.txt` trên url :  [`http://memejutsu-68f6f11c4a.cyberjutsu-lab.tech/`](http://memejutsu-68f6f11c4a.cyberjutsu-lab.tech/random)
- Phát hiện có một đường dẫn Disallow là  `/build/backup.zip`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/52b8164e-d843-444e-82db-e69f8f30d300/image.png)

- Truy cập đường dẫn [`http://memejutsu-68f6f11c4a.cyberjutsu-lab.tech/](http://memejutsu-68f6f11c4a.cyberjutsu-lab.tech/random)build/backup.zip`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/c36934f3-90bc-45c6-85cd-1d2244ed9710/image.png)

- Ta có thể tải về được tập tin backup.zip, truy cập vào tập tin ta sẽ có thể xem được mã nguồn của ứng dụng Memejutsu

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/90d3916c-5809-496c-801a-f5e886fa30e3/image.png)

**Reconmendations**

- Đảm bảo các file sao lưu như `backup.zip` không nằm trong thư mục truy cập công khai của server
- Nên tránh lưu trữ các file backup trên server, đặc biệt là trên web. Thay vào đó, nên sử dụng hệ thống lưu trữ riêng biệt với cơ chết bảo mật chặt chẽ và phân quyền rõ ràng
- Không liệt kê các file nhảy cảm trong `robots.txt` . File `robots.txt` chỉ nên dùng để hướng dẫn các công cụ tìm kiếm

**References 
`https://www.webitservices.com/blog/best-practices-protecting-data-backups/`**

### **MJ-01-002: Insecure Direct Object Reference (IDOR) Personal information Disclosure [ Medium]**

**Description and impact**

- Trên đường dẫn  [`http://memejutsu-68f6f11c4a.cyberjutsu-lab.tech/](http://memejutsu-68f6f11c4a.cyberjutsu-lab.tech/random)u/{user}` có thể thay đổi giá trị `{user}` thành `admin` và có thể truy cập vào trang cá nhân của `admin` .Ngoài ra ở phần tạo tài khoản cá nhân người dùng có thể tạo tên đăng nhập trùng với người khác chẳng hạn như `admin` và sau khi tạo xong người dùng vào trang cá nhân sẽ hiện lên trang cá nhân của `admin` . Những  kẻ tấn công có thể lợi dụng lỗ hổng để truy cập vào dữ liệu mà họ không có quyền truy cập.

**Step to reproduce**

- Tạo trang cá nhân có tên là `admin`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/f5cd8022-5938-4267-a74e-955d1938ded0/image.png)

- Truy cập vào trang cá nhân của chính bản thân

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/e7552036-f33c-4490-aa47-c8b0cb7f7685/image.png)

- Ta vào thẳng trang cá nhân của admin

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/61f1c17d-29ba-45f6-8243-cdd8a3048945/image.png)

Hoặc ta tạo một trang cá nhân lấy tên khác admin 

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/13d568a8-f986-42e0-8846-bfde2cfc9782/image.png)

- Vào trang cá nhân và kiểm tra url ta thấy có đường dẫn `/u/test`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/62a32136-1f34-48cf-ade4-3b25f1bfb5c4/image.png)

- Sau đó thay đổi  `/u/{user}` thành `admin` và ta có thể truy cập và trang admin

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/6e76ed35-4335-4707-8689-d680fea5cb79/image.png)

- Dẫn đến có thể đọc được dữ liệu không được truy cập

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/5fc79163-44db-45a8-9831-1cb13caa2a20/image.png)

**Recommendations**

- Kiểm tra quyền truy cập trực tiếp vào từng đối tượng mà người dùng muốn truy cập, chỉ có quyền truy cập vào các tài nguyên mà họ được phép
- Sử dụng Token hoặc UUID thay vì sử dụng các đường dẫn đơn giản để giá trị trở nên khó khăn hơn trong việc truy tìm. Đồng thời để tránh việc vô tình truy cập vào trang cá nhân khác

**Reference**

[`https://www.imperva.com/learn/application-security/insecure-direct-object-reference-idor/`](https://www.imperva.com/learn/application-security/insecure-direct-object-reference-idor/)

### **MJ-01-003:SQL Injection (SQLI) leading to read all database [ HIGH ]**

**Description and impact**

- Trang web Memejutsu, tại mục `Search` người dùng có thể tìm kiếm những tiêu đề của nội dung người dùng upload lên tại trang chủ của trang web . Những kẻ tấn công có thể tận dụng những câu lệnh SQL để khai thác lỗi SQLI để  khai thác thêm nhiều dữ liệu trong database

**Root cause analysis**

- Kiểm tra mã nguồn ta lấy được trong file `core-service/app/Http/Controllers/SearchController.php` và phân tích

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/0a41ede2-edbc-4688-8524-afff88a664cc/image.png)

- Đoạn mã PHP trên định nghĩa một hàm có tên là `SearchController` để tìm kiếm người dùng và bài viết từ cơ sở dử liệu dựa trên dầu vào `$search`
- Đoạn code có thể bị lỗi SQL Injection (SQLI) nếu gía trị của `$search` trong thanh tìm kiếm không được kiểm tra và xử lý đúng cách đước khi vào trong câu truy vấn SQL
- Trong đoạn mã trên, giá trị của tham số `$search` được lấy từ câu query và sử dụng để truy vấn tìm kiếm thông tin người dùng thêm vào
- Việc truyền trực tiếp tham số `$search` vào câu truy vấn SQL tạo lỗ hổng bảo mật. Nếu người dùng nhập vào thanh tìm kiếm với những câu truy vấn SQL độc hại , kẻ tấn công có thể thực hiện SQL injection

**Step to reproduce**

- Trước tiên ta tiến hành thử dùng lệnh để kiểm tra phiên bản postgresql, biết được từ mã nguồn rằng có 5 cột trong database, vì vậy ta tiến hành thử nghiệm bằng payload sau :

`')%20union%20select%20null,version(),null,null,null--+";`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/443255af-771c-4186-a1fe-ffe8a5952036/image.png)

- Ta tiến hành khai thác tiếp SQLi để lấy được tên database là gì bằng payload :

`')%20union%20select%20null,current_database(),null,null,null--+";`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/9ddf228f-4630-47cf-83db-f31128bd178c/image.png)

- Ta biết được `database` hiện tại là `memejutsu` .Tiếp theo ta xem `tables` của database là gì bằng payload

`')%20union%20select%20null,tablename,null,null,null%20from%20pg_tables%20where%20schemaname%20='public'--+";`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/4865e6a6-b2eb-4d62-9aae-54d0195524bb/image.png)

- Từ đây ta biết được bên trong database có `tables` là `flag` Tiếp theo ta check xem `column_name` bên trong `tables` bằng payload:

`')%20union%20select%20null,column_name,null,null,null%20from%20information_schema.columns%20where%20table_name%20='flag'--+";`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/1418c6d6-d221-44a0-97ba-9ea854e280d0/image.png)

- Cuối dùng ta lấy `column_name` để đọc được flag ta sử dụng payload sau :

`')%20union%20select%20null,content,null,null,null%20from%20flag--+";`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/da35577e-cba7-470c-b7bc-8b7e6fad37fc/image.png)

**Recommendations**

- Kiểm tra và sử lý đúng cách giá trị của tham số `$search` trước khi sử dụng nó trong câu truy vấn SQL. Sử dụng các phương pháp an toàn như prepared statements hoặc parameterized queries để truyền tham số vào câu truy vấn SQL
- Sử dụng các thư viện hoặc bộ công cụ bảo mật như Eloquent hoặc Doctrine.
- Theo dõi và ghi nhật ký các yêu cầu truy vấn bất thường để phát hiện và ngăn chặn các cuộc tấn công

**References**

[`https://www.hacksplaining.com/signup?next=/prevention/sql-injection`](https://www.hacksplaining.com/signup?next=/prevention/sql-injection)

### **MJ-01-004:Path Traversal Due to Insecure Cookie Serialization [ HIGH ]**

**Description and impact**

- Trang web Memejutsu có đường dẫn `/random` cho phép chúng ta chọn ngẫu hiên bất kì hình ảnh  và kèm theo tiêu đề. Trang web tạo một `cookies` có tên là `random_session` được `serialize` và mã hóa `base64` . Những kẻ tấn công có thể thay đổi đoạn `cookies` và sau đó có thể đọc được những thông tin nhạy cảm trên server

**Root cause analysis**

- Kiểm tra mã nguồn ta lấy được ở trong file `core-service/app/Http/Controllers/RandomController.php`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/27a3136a-46e5-4db5-b0b0-5307a14a7125/image.png)

- Nếu cookie không tồn tại, một đối tượng mới cảu `RandomPost` được tạo ra, serialized và lưu vào cookie

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/5c579e78-766f-4f6b-b7fc-9b21254ac064/image.png)

Ta cùng phân tích bên trong file  `core-service/app/Http/Controllers/Cookie/RandomPost.php` 

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/d109b36b-435f-46a3-ac9b-ae0830649b8e/image.png)

Phương thức tĩnh lấy meme ngẫu nhiên từ hàm `getMeme` 

Phương thức này gọi API để lấy danh sách meme và chọn một meme ngẫu nhiên

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/71ebef5c-3747-4513-bd29-f099e87c0c8d/image.png)

- Việc sử dụng `file_get_contents` có thể dẫn đến lỗi bảo mật Path Traversal . Nội dung của `file_get_contents` rơi thẳng vào `random_session` dẫn đến  Path traversal

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/c50c3201-f47c-4ca0-8fb6-6e2ff6f11160/image.png)

- Nếu mà kẻ tấn công decode base64 và sau đó chèn một đoạn Path Traversal vào trong đoạn serialize có thể đẫn dến đọc được các file nhạy cảm như `/etc/passwd`

**Step to reproduce**

- Ta tiến kiểm tra decode cookies `random_session` để kiểm tra thì ta thấy được một đoạn url `https://i.imgflip.com/{number_id}.jpg` để tạo một hình ảnh meme mới.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/e89c8b02-865e-4ba4-8638-90250800757a/image.png)

- Ta tiến hành khai thác Path Traversal  bằng payload sau :

`18:"file:///etc/passwd";`

- Sau đó ta sẽ decode `base64` và sẽ gửi vào `random_session`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/d73c5be0-5043-4170-90ef-d73b7af8274a/image.png)

- Kêt quả trả về nằm trong một đoạn `base64` và encode lại và đọc được file `/etc/passwd`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/a0815a8d-2f4d-410a-a28d-26ad2576f768/image.png)

**Recommendations**

- Xác thực đầu vào từ tham số `url` trước khi được truyền vào `file_get_contents()`
- Nếu dữ liệu nhạy cảm được lưu trữ trong cookie, hãy xem xét xác thực người dùng để ngăn chặn người dùng không có quyền truy cập vào dữ liệu của người khác.
- Việc sử dụng `unserialize()` trực tiếp trên dữ liệu cookie mà không có kiểm tra có thể dẫn đến lỗ hổng bảo mật (Insecure Deserialization).

**References**

[`https://www.imperva.com/learn/application-security/directory-traversal/`](https://www.imperva.com/learn/application-security/directory-traversal/)

### **MJ-01-005: Blind OS Command Injection to RCE [ Critical ]**

**Description and impact**

- Trên server [`http://images.memejutsu-68f6f11c4a.cyberjutsu-lab.tech/`](http://images.memejutsu-68f6f11c4a.cyberjutsu-lab.tech/) có chức năng `upload_image` và `upload_avatar` từ phía người dùng. Giá trị `foar` để giữ nguyên tỷ lệ khi thay đổi kích thước của bức ảnh rơi vào hàm `os.system`  . Nhiều khả năng kẻ tấn công có thể sẽ sử dụng các lệnh OS Command khai thác Command Injection dẫn đến RCE được

**Root cause analysis**

- Ta phân tích đoạn mã nguồn có trên file `image-service/app/main.py`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/7c0e8690-c0a9-44c2-ba4c-4abbf28e87e3/image.png)

- Đoạn mã sẽ check một đoạn mã token và tạo các tham số
- Đoạn mã trong python trên định nghĩa hàm `upload_image` để upload hình ảnh lên server
- Đoạn code có thể bị lỗi OS Command Injection nếu giá trị của `foar` không được kiểm tra và xử lý đúng cách trước khi vào hàm `os.system`
- Việc truyền giá trị vào trực tiếp gây ra lỗ hổng bảo mật. Nếu người dùng nhập giá trị `foar` chứa các câu lệnh `OS Command`

**Step to reproduce**

- Ta sẽ kiểm tra `token` bằng lỗi Path Traversal mà ta đã khai thác trên thì phát hiện được token nằm ở file `/var/www/html/.env.example`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/949e3008-1185-4a65-89e2-4ba6b21673ff/image.png)

- Ta tìm thấy đoạn `token` thông qua Path Traversal

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/dce9e817-305b-4c3d-8b24-f77ea93d69a1/image.png)

- Ta tiến hành chứng minh xem giá trị `foar` có bị lỗi OS Command Injection không bằng payload `;`sleep10`;` kết hợp với Token mà ta đã lấy được lúc nãy

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/cefcc298-2638-4035-a72d-086387e97fef/image.png)

- Kết quả trả về từ server là 21 giây . Ta tiến hành đọc flag bằng cách tạo một file `.png` thông qua chức năng `upload_image` từ server bằng payload :
- `;`cat /flag* > /storage/user/{id}/{}.png`;`

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/0210f880-e6a8-4f30-ab1e-636dd92d60c6/image.png)

- Sau đó ta tiến hành truy cập vào file ảnh mà ta mới vừa tạo

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/f5a50476-3c74-4751-b721-f663545ceac9/175d2281-4cd8-4477-8e49-98f12f72e79e/image.png)

**Recommendations**

- Tránh sử dụng `os.system` , thay vào đó sử dụng thư viện Python để xử lý. Có thể thay thể sử dụng `subprocess` để tránh OS Command Injection
- Xác thực đầu vào giá trị để đảm bảo nhỉ nhận những giá trị hợp lệ
- Ghi nhật ký xác thực để theo dõi các hành vi đáng ngờ

**References** 

https://snyk.io/blog/command-injection-python-prevention-examples/
