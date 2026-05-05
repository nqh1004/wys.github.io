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

## 🔍 Vulnerability Summary

| 🔴 Critical | 🟠 High | 🟡 Medium | 🟢 Low | ⚪ None |
|------------|--------|----------|--------|--------|
|     1      |   2    |    2     |   0    |   0    |

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
<img width="878" height="241" alt="image" src="https://github.com/user-attachments/assets/c10fd847-f90f-4642-a24c-4ea507f5a7b9" />



- Truy cập đường dẫn [`http://memejutsu-68f6f11c4a.cyberjutsu-lab.tech/](http://memejutsu-68f6f11c4a.cyberjutsu-lab.tech/random)build/backup.zip`

<img width="573" height="135" alt="image" src="https://github.com/user-attachments/assets/dd180500-61c5-4994-972b-d74c49b7df30" />

- Ta có thể tải về được tập tin backup.zip, truy cập vào tập tin ta sẽ có thể xem được mã nguồn của ứng dụng Memejutsu

<img width="908" height="195" alt="image" src="https://github.com/user-attachments/assets/df7092d5-75e2-4dfe-879b-81c2f6b48ffd" />


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

<img width="779" height="721" alt="image" src="https://github.com/user-attachments/assets/efd107d1-b21d-4200-8155-0108e92b938c" />


- Truy cập vào trang cá nhân của chính bản thân

<img width="312" height="287" alt="image" src="https://github.com/user-attachments/assets/486a8703-62e4-416a-acfd-f51e39e4b3aa" />


- Ta vào thẳng trang cá nhân của admin

<img width="1871" height="612" alt="image" src="https://github.com/user-attachments/assets/2ca1f2af-fb10-45da-b2e5-c51c83ee293b" />


Hoặc ta tạo một trang cá nhân lấy tên khác admin 

<img width="797" height="714" alt="image" src="https://github.com/user-attachments/assets/70b33bf2-844f-46ac-93af-f41aabc18d2e" />


- Vào trang cá nhân và kiểm tra url ta thấy có đường dẫn `/u/test`

<img width="1919" height="672" alt="image" src="https://github.com/user-attachments/assets/bdbed8a8-e39d-4331-8123-1b00a0a27061" />



- Sau đó thay đổi  `/u/{user}` thành `admin` và ta có thể truy cập và trang admin


<img width="1862" height="718" alt="image" src="https://github.com/user-attachments/assets/b4979f90-6590-4073-8e50-0fe8e1f5caf8" />


- Dẫn đến có thể đọc được dữ liệu không được truy cập

<img width="1523" height="782" alt="image" src="https://github.com/user-attachments/assets/a92ba9ad-9f63-4d4f-b7af-52c3c2534353" />


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

<img width="1275" height="462" alt="image" src="https://github.com/user-attachments/assets/11b354db-0b26-44ed-a2d3-5d206b0bf558" />


- Đoạn mã PHP trên định nghĩa một hàm có tên là `SearchController` để tìm kiếm người dùng và bài viết từ cơ sở dử liệu dựa trên dầu vào `$search`
- Đoạn code có thể bị lỗi SQL Injection (SQLI) nếu gía trị của `$search` trong thanh tìm kiếm không được kiểm tra và xử lý đúng cách đước khi vào trong câu truy vấn SQL
- Trong đoạn mã trên, giá trị của tham số `$search` được lấy từ câu query và sử dụng để truy vấn tìm kiếm thông tin người dùng thêm vào
- Việc truyền trực tiếp tham số `$search` vào câu truy vấn SQL tạo lỗ hổng bảo mật. Nếu người dùng nhập vào thanh tìm kiếm với những câu truy vấn SQL độc hại , kẻ tấn công có thể thực hiện SQL injection

**Step to reproduce**

- Trước tiên ta tiến hành thử dùng lệnh để kiểm tra phiên bản postgresql, biết được từ mã nguồn rằng có 5 cột trong database, vì vậy ta tiến hành thử nghiệm bằng payload sau :

`')%20union%20select%20null,version(),null,null,null--+";`

<img width="1889" height="606" alt="image" src="https://github.com/user-attachments/assets/d22c20ad-bef2-41a1-a60b-2113cb25287b" />


- Ta tiến hành khai thác tiếp SQLi để lấy được tên database là gì bằng payload :

`')%20union%20select%20null,current_database(),null,null,null--+";`

<img width="1881" height="842" alt="image" src="https://github.com/user-attachments/assets/f5d41a9f-d086-4618-bc2f-ce76eba6a018" />


- Ta biết được `database` hiện tại là `memejutsu` .Tiếp theo ta xem `tables` của database là gì bằng payload

`')%20union%20select%20null,tablename,null,null,null%20from%20pg_tables%20where%20schemaname%20='public'--+";`

<img width="1917" height="807" alt="image" src="https://github.com/user-attachments/assets/77ac799f-43ac-4511-bafe-cb9d6db59eba" />


- Từ đây ta biết được bên trong database có `tables` là `flag` Tiếp theo ta check xem `column_name` bên trong `tables` bằng payload:

`')%20union%20select%20null,column_name,null,null,null%20from%20information_schema.columns%20where%20table_name%20='flag'--+";`

<img width="1893" height="801" alt="image" src="https://github.com/user-attachments/assets/483f8cc0-c61f-428d-8c6d-f5b92e35ed0d" />


- Cuối dùng ta lấy `column_name` để đọc được flag ta sử dụng payload sau :

`')%20union%20select%20null,content,null,null,null%20from%20flag--+";`

<img width="1888" height="783" alt="image" src="https://github.com/user-attachments/assets/4d0d2fdb-f0c4-4f38-b7c5-4474c42b63d5" />


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

<img width="775" height="412" alt="image" src="https://github.com/user-attachments/assets/fcc297e2-f9b9-429d-b539-0576e05f49d1" />


- Nếu cookie không tồn tại, một đối tượng mới cảu `RandomPost` được tạo ra, serialized và lưu vào cookie

<img width="772" height="540" alt="image" src="https://github.com/user-attachments/assets/ec7a9e4b-6753-4373-a51a-ab65d876a232" />


Ta cùng phân tích bên trong file  `core-service/app/Http/Controllers/Cookie/RandomPost.php` 

<img width="773" height="930" alt="image" src="https://github.com/user-attachments/assets/e4c35d45-1beb-43f4-9f3a-25bb2c5b7824" />


Phương thức tĩnh lấy meme ngẫu nhiên từ hàm `getMeme` 

Phương thức này gọi API để lấy danh sách meme và chọn một meme ngẫu nhiên

<img width="669" height="256" alt="image" src="https://github.com/user-attachments/assets/50d187e0-26f7-4dcc-847b-1ed0361dc74b" />


- Việc sử dụng `file_get_contents` có thể dẫn đến lỗi bảo mật Path Traversal . Nội dung của `file_get_contents` rơi thẳng vào `random_session` dẫn đến  Path traversal

<img width="844" height="402" alt="image" src="https://github.com/user-attachments/assets/e1ad4dbd-0350-4ad8-9b48-b2b90e5270fa" />


- Nếu mà kẻ tấn công decode base64 và sau đó chèn một đoạn Path Traversal vào trong đoạn serialize có thể đẫn dến đọc được các file nhạy cảm như `/etc/passwd`

**Step to reproduce**

- Ta tiến kiểm tra decode cookies `random_session` để kiểm tra thì ta thấy được một đoạn url `https://i.imgflip.com/{number_id}.jpg` để tạo một hình ảnh meme mới.

<img width="1719" height="435" alt="image" src="https://github.com/user-attachments/assets/7b329e1a-79d7-4e69-8b86-44b2979141e6" />


- Ta tiến hành khai thác Path Traversal  bằng payload sau :

`18:"file:///etc/passwd";`

- Sau đó ta sẽ decode `base64` và sẽ gửi vào `random_session`

<img width="1675" height="621" alt="image" src="https://github.com/user-attachments/assets/61768dc2-b35d-4715-9474-eac770325e76" />


- Kêt quả trả về nằm trong một đoạn `base64` và encode lại và đọc được file `/etc/passwd`

<img width="1881" height="785" alt="image" src="https://github.com/user-attachments/assets/7256206d-6e61-471c-be74-9c911163e615" />

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

<img width="1476" height="802" alt="image" src="https://github.com/user-attachments/assets/53a8bb02-d97a-4a93-988c-18c5d6a9d742" />


- Đoạn mã sẽ check một đoạn mã token và tạo các tham số
- Đoạn mã trong python trên định nghĩa hàm `upload_image` để upload hình ảnh lên server
- Đoạn code có thể bị lỗi OS Command Injection nếu giá trị của `foar` không được kiểm tra và xử lý đúng cách trước khi vào hàm `os.system`
- Việc truyền giá trị vào trực tiếp gây ra lỗ hổng bảo mật. Nếu người dùng nhập giá trị `foar` chứa các câu lệnh `OS Command`

**Step to reproduce**

- Ta sẽ kiểm tra `token` bằng lỗi Path Traversal mà ta đã khai thác trên thì phát hiện được token nằm ở file `/var/www/html/.env.example`

<img width="1920" height="620" alt="image" src="https://github.com/user-attachments/assets/6143ced8-2749-47f8-b2e4-3c51b1987122" />


- Ta tìm thấy đoạn `token` thông qua Path Traversal

<img width="1881" height="805" alt="image" src="https://github.com/user-attachments/assets/bc72fa27-a193-4074-9da8-2b7bbd508df6" />


- Ta tiến hành chứng minh xem giá trị `foar` có bị lỗi OS Command Injection không bằng payload `;`sleep10`;` kết hợp với Token mà ta đã lấy được lúc nãy

<img width="1920" height="846" alt="image" src="https://github.com/user-attachments/assets/d39fcbb6-4682-4636-95a0-7cdf9e01ffe6" />


- Kết quả trả về từ server là 21 giây . Ta tiến hành đọc flag bằng cách tạo một file `.png` thông qua chức năng `upload_image` từ server bằng payload :
- `;`cat /flag* > /storage/user/{id}/{}.png`;`

<img width="1524" height="419" alt="image" src="https://github.com/user-attachments/assets/89bb5c85-40c1-44d0-a154-d3b4b05d02e2" />


- Sau đó ta tiến hành truy cập vào file ảnh mà ta mới vừa tạo

<img width="1888" height="547" alt="image" src="https://github.com/user-attachments/assets/a48785dd-e538-483f-98df-05cd28eb80ff" />


**Recommendations**

- Tránh sử dụng `os.system` , thay vào đó sử dụng thư viện Python để xử lý. Có thể thay thể sử dụng `subprocess` để tránh OS Command Injection
- Xác thực đầu vào giá trị để đảm bảo nhỉ nhận những giá trị hợp lệ
- Ghi nhật ký xác thực để theo dõi các hành vi đáng ngờ

**References** 

https://snyk.io/blog/command-injection-python-prevention-examples/
